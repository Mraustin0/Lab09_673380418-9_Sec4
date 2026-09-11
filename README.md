# Lab 9: Spring Boot - Transaction

ระบบฝากเงินอย่างง่าย ใช้ทดสอบการทำงานของ `@Transactional` (COMMIT / ROLLBACK)

## โครงสร้างโปรเจกต์

```
src/main/java/com/example/lab9
├── controller
│   └── AccountController.java
├── service
│   ├── AccountService.java
│   └── DepositService.java   ← มี @Transactional บนเมธอด deposit()
├── repository
│   ├── AccountRepository.java
│   └── DepositRepository.java
└── model
    ├── Account.java
    ├── DepositTransaction.java
    └── DepositRequest.java
```

## เตรียมฐานข้อมูล

```sql
CREATE DATABASE lab9;
```

แก้ `src/main/resources/application.properties` ให้ username/password ตรงกับ PostgreSQL ของเครื่องตัวเอง (และแก้ port ถ้าไม่ใช่ 5432)

## รันโปรเจกต์

```
./mvnw spring-boot:run
```

รอจนขึ้นข้อความ `Started Lab9Application`

## Endpoint

| Method | URL | Body |
|---|---|---|
| POST | /accounts | `{"accountNumber": "1234567890", "ownerName": "John", "balance": 0}` |
| GET | /accounts/{id} | - |
| POST | /accounts/{id}/deposit | `{"amount": 1000}` |

## ทดสอบ Rollback (ข้อ 12–13)

1. เปิดไฟล์ `DepositService.java` แล้ว uncomment บรรทัด `throw new RuntimeException("Test Rollback");` ที่ท้ายเมธอด `deposit()`
2. หยุดโปรแกรม (Ctrl+C) แล้วรันใหม่ด้วย `./mvnw spring-boot:run`
3. ยิง `POST /accounts/{id}/deposit` อีกครั้ง ควรได้ `500 Internal Server Error`
4. เช็ค `GET /accounts/{id}` → balance ต้องไม่เพิ่ม, และตาราง `deposit_transaction` ใน pgAdmin ต้องไม่มีแถวใหม่ (เพราะ `@Transactional` สั่ง ROLLBACK)
5. ลองลบ `@Transactional` ออกจาก `deposit()` แล้วทำซ้ำ → คราวนี้ balance จะเพิ่มค้างอยู่ (ไม่มี rollback)
6. ทำเสร็จแล้ว **อย่าลืม** comment บรรทัด `throw new RuntimeException(...)` กลับ และใส่ `@Transactional` คืนก่อนส่งงาน (ไฟล์ที่แนบมานี้อยู่ในสถานะปกติแล้ว — บรรทัด throw ถูก comment ไว้)
# Lab09_673380418-9_Sec4
