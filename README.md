# 🛡️ Admin Service

Service สำหรับจัดการระบบหลังบ้าน (Admin Panel) ของโปรเจกต์ **GameGear E-commerce**

---

## 🏛️ Architectural Design

`admin-service` ถูกออกแบบให้เป็น **"ผู้ประสานงาน" (Coordinator)** หรือ **"Admin Gateway"** โดย **ไม่มีฐานข้อมูลเป็นของตัวเอง** หน้าที่หลักคือ:

* ยืนยันตัวตนของ Admin
* ประสานงานกับ Service อื่น ๆ ผ่าน API ของพวกมัน โดยไม่ไปยุ่งกับข้อมูลโดยตรง

**ตัวอย่าง:**

* เพิ่มสินค้า → เรียกใช้ API ของ **shop-service**
* จัดการผู้ใช้ → เรียกใช้ API ของ **users-service**

แนวทางนี้ช่วยให้การเป็นเจ้าของข้อมูลยังคงอยู่กับ Service ที่รับผิดชอบ และแยก Logic ฝั่ง Admin ออกมาเฉพาะ

---

## ✨ Features & Responsibilities

ฟีเจอร์หลักของ `admin-service` 4 ส่วน ได้แก่:

* **Admin Authentication** (`/api/admin/auth/*`)

  * จัดการการเข้าสู่ระบบ Admin
  * สร้าง JWT พร้อม Permissions สำหรับ Admin

* **Product Management** (`/api/admin/products/*`)

  * เพิ่ม, แก้ไข, ลบสินค้า
  * ใช้ API ของ **shop-service**

* **Order Management** (`/api/admin/orders/*`)

  * ดูรายการคำสั่งซื้อทั้งหมด
  * อัปเดตสถานะคำสั่งซื้อ (`pending`, `shipped`, `completed`)
  * ใช้ API ของ **shop-service**

* **User Management** (`/api/admin/users/*`)

  * ดูรายชื่อผู้ใช้ทั้งหมด
  * จัดการสิทธิ์และสถานะผู้ใช้
  * ใช้ API ของ **users-service**

---

## 📂 Project Structure

โครงสร้างใกล้เคียงกับ Service อื่น ๆ แต่จะมี **clients** สำหรับติดต่อกับ Service ภายนอก

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
├── .env.example
├── .gitignore
├── go.mod
└── README.md
</pre>
</td>
<td>
<ul>
<li><b>cmd/api</b>: จุดเริ่มต้นการรันโปรแกรม</li>
<li><b>internal</b>: โค้ดหลักของ Service</li>
<ul>
<li><b>handlers</b>: รับ Request จาก Admin Panel</li>
<li><b>services</b>: จัดการ Logic และเรียกใช้ Service อื่น</li>
<li><b>clients</b>: โค้ดสำหรับยิง HTTP Request ไปยัง Service อื่น</li>
</ul>
<li><b>.env.example</b>: ตัวอย่างไฟล์ Configuration</li>
<li><b>.gitignore</b>: รายการไฟล์ที่ไม่ต้อง push ขึ้น Git</li>
</ul>
</td>
</tr>
</table>

---

## 🚀 Getting Started (Step-by-step)

### Step 1 — Clone the Repository

```bash
git clone https://github.com/Wattanaroj2567/admin-service.git
cd admin-service
```

### Step 1 (Alt) — Clone Develop Branch

```bash
git clone -b develop https://github.com/Wattanaroj2567/admin-service.git
cd admin-service
```

### Step 2 — Install Dependencies

```bash
go mod tidy
```

### Step 3 — Configure Environment Variables

สร้างไฟล์ `.env` และใส่ค่า URL ของ Service อื่น ๆ

```env
# Core Configuration
APPLICATION_PORT=8082

# URLs for other microservices
USER_SERVICE_URL="http://localhost:8080"
SHOP_SERVICE_URL="http://localhost:8081"
```

### Step 4 — Run the Service

```bash
go run cmd/api/main.go
```

> Service จะเริ่มที่ `http://localhost:8082`

---

## 📝 API Documentation: Swagger (OpenAPI)

### Step 1 — ติดตั้ง `swag`

```bash
go install github.com/swaggo/swag/cmd/swag@latest
```

### Step 2 — สร้างไฟล์ Swagger Docs

```bash
swag init
```

> จะสร้างโฟลเดอร์ `docs` และไฟล์ที่จำเป็นอัตโนมัติ

### Step 3 — เปิดดู API Docs

```
http://localhost:8082/swagger/index.html
```

---

## 🤝 Remote Development (ngrok)

`admin-service` ต้องอาศัย **users-service** และ **shop-service**

### Step 1 — ขอ ngrok URL จากเพื่อน

* เพื่อนที่รัน `users-service` → ส่ง URL มาให้
* เพื่อนที่รัน `shop-service` → ส่ง URL มาให้

```bash
ngrok http <PORT>
```

*(เช่น `ngrok http 8080` สำหรับ users-service)*

### Step 2 — อัปเดต `.env`

```env
USER_SERVICE_URL="<FRIEND_A_NGROK_URL>"
SHOP_SERVICE_URL="<FRIEND_B_NGROK_URL>"
```

### Step 3 — Run Admin Service

```bash
go run cmd/api/main.go
```
