# سند معماری نرم‌افزار — رستوران‌یار (RestaurantYar)

| مشخصات | مقدار |
|--------|-------|
| **نام پروژه** | رستوران‌یار (RestaurantYar) |
| **نسخه سند** | ۱.۰ |
| **تاریخ** | ۱۴۰۵/۰۷/۲۶ |
| **تهیه‌کنندگان** | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |
| **وضعیت** | پیش‌نویس |

---

## ۱. مقدمه

### ۱.۱. هدف سند

این سند معماری نرم‌افزار پروژه **رستوران‌یار** را تعریف می‌کند. هدف آن ایجاد یک درک مشترک از ساختار سیستم، تصمیم‌های کلیدی معماری و محدودیت‌های موجود است.

### ۱.۲. دامنه

**در دامنه:**

- معماری سطح بالا
- لایه‌های سیستم
- Endpointها
- امنیت
- جریان داده

**خارج از دامنه:**

- جزئیات پیاده‌سازی
- کدنویسی
- طراحی رابط کاربری (UI)

### ۱.۳. ذی‌نفعان

| ذی‌نفع | نقش |
|--------|------|
| تیم توسعه | پیاده‌سازی سیستم |
| استاد درس | ارزیابی پروژه |
| مشتری فرضی (آقای کریمی) | تأیید سیستم |

---

## ۲. نمای کلی معماری

### ۲.۱. سبک معماری

**Monolith + Layered Architecture**

ویژگی‌های این معماری:

- یک برنامه واحد
- سه لایه اصلی شامل Presentation، Business و Data
- مناسب برای تیم کوچک سه‌نفره
- ساده برای شروع و قابل گسترش در آینده

### ۲.۲. دیاگرام لایه‌ها

```
┌─────────────────────────────────────────┐
│         Presentation Layer              │
│  React (SPA) — مشتری، گارسون، مدیر     │
└─────────────────┬───────────────────────┘
                  │ HTTP/REST
┌─────────────────▼───────────────────────┐
│          Business Layer                 │
│  Python + FastAPI — منطق سفارش،         │
│  انبار، تخفیف، حقوق                     │
└─────────────────┬───────────────────────┘
                  │ SQL
┌─────────────────▼───────────────────────┐
│            Data Layer                   │
│           PostgreSQL                    │
└─────────────────────────────────────────┘
```

### ۲.۳. جریان داده (نمونه: ثبت سفارش)

1. مشتری QR روی میز را با موبایل اسکن می‌کند.
2. Frontend درخواست `POST /api/orders` را به Backend ارسال می‌کند.
3. Business Layer اعتبارسنجی و محاسبه هزینه را انجام می‌دهد.
4. موجودی مواد اولیه در انبار کاهش می‌یابد.
5. سفارش در پایگاه داده ثبت می‌شود.
6. اعلان به آشپزخانه (KDS) و پیک ارسال می‌شود.
7. پاسخ موفقیت‌آمیز به مشتری بازگردانده می‌شود.

---

## ۳. لایه‌های معماری

### ۳.۱. Presentation Layer (Frontend)

| مورد | انتخاب |
|------|--------|
| فناوری | React 18 |
| Routing | React Router |
| State Management | Redux Toolkit |
| UI | Tailwind CSS |
| Build Tool | Vite |
| ارتباط با API | Axios |

> **نکته:** رابط کاربری باید واکنش‌گرا (Responsive) باشد؛ زیرا مشتری، گارسون، آشپز و پیک از دستگاه‌های مختلف استفاده می‌کنند.

### ۳.۲. Business Layer (Backend)

| مورد | انتخاب |
|------|--------|
| فناوری | Python 3.12 + FastAPI |
| ORM | SQLAlchemy |
| Validation | Pydantic |
| Authentication | python-jose (JWT) |
| Password Hashing | passlib[bcrypt] |
| Logging | Loguru |
| Real-time Communication | python-socketio |

### ۳.۳. Data Layer (Database)

| مورد | انتخاب |
|------|--------|
| فناوری | PostgreSQL 16 |
| Migration | Alembic |
| Backup | روزانه |
| Hosting | Railway |

---

## ۴. تصمیم‌های معماری (ADR)

### ADR-01: چرا Monolith؟

**تصمیم:** استفاده از معماری Monolith

**دلایل:**

- تیم کوچک (۳ نفر)
- پیچیدگی کم
- سرعت توسعه بالاتر
- استقرار ساده‌تر
- مناسب برای نسخه اولیه محصول (MVP)
- امکان مهاجرت به Microservices در آینده

### ADR-02: چرا FastAPI (نه Django)؟

**تصمیم:** استفاده از FastAPI برای Backend

**دلایل:**

- سرعت بالا و پشتیبانی از Async
- مستندات خودکار (Swagger UI)
- معماری API-first و مناسب برای پروژه
- استفاده از Pydantic برای اعتبارسنجی قوی
- یادگیری ساده‌تر برای تیم دانشجویی

### ADR-03: چرا PostgreSQL؟

**تصمیم:** استفاده از PostgreSQL برای Database

**دلایل:**

- پایگاه داده رابطه‌ای قدرتمند
- پشتیبانی از JSON برای تنظیمات پویا
- رایگان و متن‌باز (Open Source)
- پشتیبانی از تراکنش برای عملیات مالی

### ADR-04: چرا Socket.io برای اعلان‌ها؟

**تصمیم:** استفاده از python-socketio برای اعلان‌های بلادرنگ

**دلایل:**

- نمایشگر آشپزخانه باید سفارش جدید را بلافاصله دریافت کند.
- پیک باید اعلان آماده بودن سفارش را بلافاصله دریافت کند.
- مشتری باید تغییر وضعیت سفارش را به‌صورت بلادرنگ مشاهده کند.

### ADR-05: چرا bcrypt برای رمز عبور؟

**تصمیم:** استفاده از bcrypt برای هش کردن رمزهای عبور

**دلایل:**

- استاندارد مناسب برای ذخیره امن رمزهای عبور
- استفاده از Salt تصادفی برای هر کاربر
- ضریب هزینه قابل تنظیم (۱۲)
- مقاومت در برابر حملات Brute Force و Rainbow Table

---

## ۵. REST API

### ۵.۱. Endpointهای عمومی (بدون احراز هویت)

| Method | Endpoint | توضیحات |
|--------|----------|----------|
| GET | `/api/menu` | دریافت لیست منو با دسته‌بندی |
| GET | `/api/menu/{id}` | دریافت جزئیات یک غذا |
| GET | `/api/categories` | دریافت لیست دسته‌بندی‌ها |
| GET | `/api/menu/search` | جستجو در منو |
| POST | `/api/auth/register` | ثبت‌نام |
| POST | `/api/auth/login` | ورود |

### ۵.۲. Endpointهای مشتری

| Method | Endpoint | توضیحات |
|--------|----------|----------|
| POST | `/api/orders` | ثبت سفارش جدید |
| GET | `/api/orders/{id}` | دریافت جزئیات سفارش |
| GET | `/api/orders/{id}/status` | پیگیری وضعیت سفارش |
| POST | `/api/tables/reserve` | رزرو میز |
| POST | `/api/coupons/validate` | اعتبارسنجی کد تخفیف |
| POST | `/api/orders/{id}/review` | ثبت نظر و امتیاز |

### ۵.۳. Endpointهای گارسون

| Method | Endpoint | توضیحات |
|--------|----------|----------|
| GET | `/api/tables` | دریافت وضعیت میزها |
| POST | `/api/orders/waiter` | ثبت سفارش حضوری |
| PUT | `/api/orders/{id}/assign-table` | اتصال سفارش به میز |
| GET | `/api/waiter/online-orders` | دریافت صف سفارش‌های آنلاین آماده |
| PUT | `/api/waiter/orders/{id}/hand-to-driver` | تحویل سفارش به پیک |
| GET | `/api/waiter/orders/{id}/delivery-info` | دریافت اطلاعات پیک و مشتری |

### ۵.۴. Endpointهای آشپزخانه (KDS)

| Method | Endpoint | توضیحات |
|--------|----------|----------|
| GET | `/api/kitchen/orders` | دریافت سفارش‌های در انتظار |
| PUT | `/api/kitchen/orders/{id}/status` | تغییر وضعیت سفارش |

### ۵.۵. Endpointهای پیک

| Method | Endpoint | توضیحات |
|--------|----------|----------|
| GET | `/api/delivery/orders` | دریافت سفارش‌های آماده برای ارسال |
| PUT | `/api/delivery/orders/{id}/accept` | پذیرش سفارش |
| PUT | `/api/delivery/orders/{id}/delivered` | ثبت تحویل سفارش |

### ۵.۶. Endpointهای انباردار

| Method | Endpoint | توضیحات |
|--------|----------|----------|
| GET | `/api/inventory` | دریافت لیست موجودی مواد اولیه |
| POST | `/api/inventory` | ثبت ورود مواد اولیه |
| PUT | `/api/inventory/{id}` | به‌روزرسانی موجودی |
| GET | `/api/inventory/low-stock` | دریافت لیست مواد کم‌موجود |

### ۵.۷. Endpointهای مدیر (Admin)

| Method | Endpoint | توضیحات |
|--------|----------|----------|
| POST | `/api/admin/menu` | افزودن غذا |
| PUT | `/api/admin/menu/{id}` | ویرایش غذا |
| DELETE | `/api/admin/menu/{id}` | حذف غذا |
| POST | `/api/admin/discount` | تعریف کد تخفیف |
| GET | `/api/admin/orders` | دریافت لیست همه سفارش‌ها |
| PUT | `/api/admin/orders/{id}/status` | تغییر وضعیت سفارش |
| POST | `/api/admin/employees` | افزودن کارمند |
| POST | `/api/admin/payroll/calculate` | محاسبه حقوق |
| GET | `/api/admin/reports/sales` | گزارش فروش |
| GET | `/api/admin/reports/delivery` | گزارش هزینه ارسال |
| GET | `/api/admin/reports/payroll` | گزارش حقوق |

### ۵.۸. نمونه Response (سفارش)

```json
{
  "id": 1024,
  "tableNumber": 7,
  "customerName": "نگار موسوی",
  "orderType": "delivery",
  "items": [
    {
      "menuId": 12,
      "name": "پیتزا مارگاریتا",
      "quantity": 2,
      "unitPrice": 320000,
      "customization": "بدون پیاز، اضافه پنیر"
    }
  ],
  "subtotal": 640000,
  "discount": 64000,
  "deliveryFee": 45000,
  "tax": 57600,
  "total": 678600,
  "status": "preparing",
  "createdAt": "1405-07-26T20:15:00"
}
```

---

## ۶. امنیت

### ۶.۱. احراز هویت (Authentication)

- استفاده از JWT برای API
- استفاده از bcrypt برای هش کردن گذرواژه‌ها (SALT_ROUNDS = 12)
- استفاده از Refresh Token برای تمدید اعتبار دسترسی

### ۶.۲. مجوزدهی (Authorization)

- استفاده از Role-Based Access Control (RBAC)
- نقش‌های سیستم:
  - Customer
  - Waiter
  - Chef
  - Cashier
  - Warehouse
  - Delivery
  - Admin
- هر نقش فقط به Endpointهای مجاز و مرتبط با وظایف خود دسترسی دارد.

### ۶.۳. امنیت API

- استفاده اجباری از HTTPS
- محدودیت تعداد درخواست‌ها (Rate Limiting: ۱۰۰ درخواست در دقیقه)
- محدودسازی CORS به دامنه Frontend
- اعتبارسنجی ورودی‌ها با Pydantic
- ثبت فعالیت‌ها و رخدادهای سیستم با Loguru

### ۶.۴. امنیت تراکنش‌های مالی

- انجام محاسبه هزینه‌ها در سمت سرور، نه کلاینت
- استفاده از تراکنش‌های پایگاه داده برای ثبت سفارش و کاهش موجودی
- جلوگیری از Race Condition در استفاده هم‌زمان از کد تخفیف

---

## ۷. مقیاس‌پذیری (Scalability)

### ۷.۱. محدودیت‌های فعلی

- معماری Monolith دارای محدودیت‌هایی در مقیاس‌پذیری مستقل اجزا است.
- استفاده از یک سرور، سیستم را با خطر تک‌نقطه شکست (Single Point of Failure) مواجه می‌کند.

### ۷.۲. مسیر رشد

1. **فاز اول (MVP):** معماری Monolith با یک سرور
2. **فاز دوم:** استفاده از Load Balancer و چند سرور
3. **فاز سوم:** پیاده‌سازی Database Replication
4. **فاز چهارم:** جداسازی سرویس‌های پرمصرف مانند KDS و اعلان‌ها
5. **فاز پنجم:** مهاجرت تدریجی به معماری Microservices

---

## ۸. ریسک‌ها و بدهی فنی

| ریسک | احتمال | اثر | راهکار |
|------|--------|-----|---------|
| ازدحام سفارش در ساعات شلوغی | بالا | متوسط | استفاده از صف پیام (Redis + Celery) |
| نفوذ امنیتی | کم | بالا | رعایت OWASP Top 10 |
| از دست رفتن داده‌ها | کم | بالا | تهیه Backup روزانه |
| قطعی اینترنت | متوسط | بالا | Cache محلی و حالت آفلاین |
| محاسبه نادرست هزینه ارسال | متوسط | بالا | تست Unit دقیق |
| محاسبه نادرست حقوق | متوسط | بالا | تست Unit دقیق |
| بدهی فنی | بالا | متوسط | Refactor در Sprint 3 |

---

## ۹. فناوری‌های مورد استفاده

| لایه | فناوری |
|------|--------|
| Frontend | React 18 + Vite + Tailwind CSS + Redux Toolkit |
| Backend | Python 3.12 + FastAPI |
| Database | PostgreSQL 16 |
| ORM | SQLAlchemy + Alembic |
| Authentication | python-jose + passlib[bcrypt] |
| Real-time | python-socketio |
| Testing | pytest + httpx |
| CI/CD | GitHub Actions |
| Deployment | Railway |

---

## ۱۰. ساختار پیشنهادی پروژه

```
restaurant-yar/
│
├── backend/                    # FastAPI
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── database.py
│   │   ├── models/             # SQLAlchemy
│   │   ├── schemas/            # Pydantic
│   │   ├── routers/            # API endpoints
│   │   ├── services/           # Business Logic
│   │   ├── utils/              # Security, Dependencies
│   │   └── sockets/            # KDS
│   │
│   ├── alembic/                # Database Migrations
│   └── requirements.txt
│
├── frontend/                   # React
│   └── src/
│
├── template/                   # Project Documents
│   ├── vision.md
│   ├── personas.md
│   ├── user-stories.md
│   ├── architecture.md
│   └── database.md
│
└── README.md
```

---

## ۱۱. تست (Testing)

| سطح تست | ابزار | هدف |
|---------|--------|------|
| Unit Test | pytest | تست منطق محاسبات (هزینه ارسال، تخفیف، حقوق) |
| Integration Test | httpx | تست Endpointها |
| E2E Test | Playwright | تست جریان کامل سفارش |

**هدف پوشش تست (Test Coverage):** حداقل ۷۰٪

---

## پیوست الف — تاریخچه تغییرات

| نسخه | تاریخ | تغییرات | تهیه‌کنندگان |
|-------|-------|----------|--------------|
| ۱.۰ | ۱۴۰۵/۰۷/۲۶ | نسخه اولیه سند معماری | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |

---
