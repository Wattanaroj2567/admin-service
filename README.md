# 🛡️ Admin Service

Service สำหรับจัดการระบบหลังบ้าน (Admin Panel) ทั้งหมดของโปรเจกต์ **GameGear E-commerce**

---

## 🏛️ Architectural Design

Service นี้ถูกออกแบบให้เป็น **"ผู้ประสานงาน" (Coordinator)** หรือ **"Admin Gateway"**
การตัดสินใจเชิงสถาปัตยกรรมที่สำคัญคือ Service นี้จะ **ไม่มีฐานข้อมูลเป็นของตัวเอง**
หน้าที่หลักของมันคือการยืนยันตัวตนของ Admin จากนั้นจะทำหน้าที่ประสานงานโดยการเรียกใช้ API ของ Service อื่นๆ ที่เป็นเจ้าของข้อมูลนั้นๆ

**ตัวอย่าง:**

* หาก Admin ต้องการเพิ่มสินค้า → `admin-service` จะเรียกใช้ API ของ **shop-service** เพื่อทำการเพิ่มข้อมูล
* หาก Admin ต้องการจัดการผู้ใช้ → `admin-service` จะเรียกใช้ API ของ **users-service** เพื่อจัดการข้อมูลผู้ใช้

วิธีนี้ช่วยแยก Logic ของฝั่ง Admin ออกมาโดยเฉพาะ ในขณะที่ยังคงเคารพการเป็นเจ้าของข้อมูลของ Service อื่นๆ

---

## ✨ Features & Responsibilities

Service นี้ทำหน้าที่เป็น Backend สำหรับผู้ดูแลระบบ โดยมีฟีเจอร์หลักดังนี้:

* **Admin Authentication** (`/api/admin/auth/*`)

  * จัดการการเข้าสู่ระบบสำหรับ Admin โดยเฉพาะ
  * สร้าง JWT ที่มีสิทธิ์ (Permissions) สำหรับ Admin

* **Product Management** (`/api/admin/products/*`)

  * เพิ่ม, แก้ไข, และลบสินค้าในระบบ
  * *(Service นี้จะเรียกใช้ **shop-service** เพื่อจัดการข้อมูลสินค้า)*

* **Order Management** (`/api/admin/orders/*`)

  * ดูรายการคำสั่งซื้อทั้งหมดในระบบ
  * อัปเดตสถานะของคำสั่งซื้อ (เช่น `pending`, `shipped`, `completed`)
  * *(Service นี้จะเรียกใช้ **shop-service** เพื่อจัดการข้อมูลคำสั่งซื้อ)*

* **User Management** (`/api/admin/users/*`)

  * ดูรายการผู้ใช้ทั้งหมด
  * จัดการสิทธิ์หรือสถานะของผู้ใช้
  * *(Service นี้จะเรียกใช้ **users-service** เพื่อจัดการข้อมูลผู้ใช้)*

---

## 📂 Project Structure

โครงสร้างของโปรเจกต์จะคล้ายกับ Service อื่นๆ แต่ส่วน **services** จะมีความพิเศษตรงที่ต้องเขียนโค้ดเพื่อสื่อสารกับ Service อื่นผ่าน HTTP Request

<table>
<tr>
<td width="60%">
<pre>
.
├── cmd/api/
│   └── main.go
├── internal/
│   ├── handlers/
│   │   └── admin_handler.go
│   ├── services/
│   │   └── admin_service.go
│   └── clients/
│       ├── user_service_client.go
│       └── shop_service_client.go
├── .env
├── go.mod
└── README.md
</pre>
</td>
<td>
<ul>
<li><b>cmd/api</b>: จุดเริ่มต้นในการรันโปรแกรม</li>
<li><b>internal</b>: โค้ดหลักทั้งหมดของ Service</li>
<ul>
<li><b>handlers</b>: ส่วนรับ Request จาก Admin Panel</li>
<li><b>services</b>: ส่วนจัดการ Logic และเรียกใช้ Service อื่น</li>
<li><b>clients</b>: (ส่วนพิเศษ) โค้ดสำหรับยิง API Request ไปยัง Service อื่นๆ</li>
</ul>
<li><b>.env</b>: ไฟล์เก็บ Configuration (เช่น URL ของ Service อื่น)</li>
</ul>
</td>
</tr>
</table>

---

## 🚀 Getting Started (Step-by-step)

ทำตามขั้นตอนทีละสเต็ปเพื่อตั้งค่าและรัน Service ในเครื่องของคุณ

### Step 1 — Clone the Repository (Standard) สำหรับใช้งานได้จริง

```bash
git clone https://github.com/Wattanaroj2567/admin-service.git
cd admin-service
```

### Step 1 (Alt) — Direct Branch Clone สำหรับผู้พัฒนาให้เริ่มต้นที่นี่เท่านั้น
คำสั่งนี้จะ Clone โปรเจกต์ทั้งหมดมาที่เครื่องของคุณ โดยจะได้ Branch develop เป็นค่าเริ่มต้น
```bash
git clone -b develop https://github.com/Wattanaroj2567/ad-service.git
cd admin-service
```

### Step 2 — Install Dependencies

```bash
go mod tidy
```

### Step 3 — Configure Environment Variables

สร้างไฟล์ `.env` และใส่ URL ของ Service อื่นๆ ที่ต้องเรียกใช้ *(Port อาจเปลี่ยนแปลงได้ขึ้นอยู่กับการตั้งค่าของแต่ละเครื่อง)*

```env
# URLs for other microservices
USER_SERVICE_URL="http://localhost:8080"
SHOP_SERVICE_URL="http://localhost:8081"
```

### Step 4 — Run the Service

```bash
go run cmd/api/main.go
```

* เซิร์ฟเวอร์จะเริ่มต้นทำงานที่ `http://localhost:8082` *(ใช้ Port ที่ไม่ซ้ำกับ Service อื่น)*

---

## 🤝 Remote Development (Working from Different Locations)

Service นี้ต้องอาศัยการทำงานของ **users-service** และ **shop-service** หากคุณต้องการทดสอบการทำงานร่วมกับ Service ของเพื่อนที่อยู่คนละสถานที่ ให้ทำตามขั้นตอนต่อไปนี้:

### Step 1 — ขอ ngrok URL จากเพื่อนร่วมทีม

* ขอ URL สาธารณะจากเพื่อนที่กำลังรัน **users-service**
* ขอ URL สาธารณะจากเพื่อนที่กำลังรัน **shop-service**
* สำหรับเพื่อนที่รัน Service อื่น: เพื่อนของคุณจะต้องใช้ **ngrok** เพื่อสร้าง URL สาธารณะ โดยรันคำสั่ง:

```bash
ngrok http <PORT>
```

*(เช่น `ngrok http 8080` สำหรับ users-service)*

### Step 2 — อัปเดตไฟล์ .env ของคุณ

นำ URL ที่ได้จากเพื่อนมาใส่ในไฟล์ `.env` ของ **admin-service** ที่คุณกำลังทำอยู่

```env
# .env file on YOUR machine
USER_SERVICE_URL="<FRIEND_A_NGROK_URL>"
SHOP_SERVICE_URL="<FRIEND_B_NGROK_URL>"
```

### Step 3 — รัน admin-service

รัน `admin-service` ของคุณตามปกติ
Service ของคุณจะสามารถเรียกใช้งาน Service ของเพื่อนผ่านอินเทอร์เน็ตได้
