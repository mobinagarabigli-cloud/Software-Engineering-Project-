# 🍽 رستوران‌یار (RestaurantYar)

سامانهٔ یکپارچهٔ مدیریت رستوران — تحلیل، طراحی و پیاده‌سازی

![وضعیت](https://img.shields.io/badge/وضعیت-در_حال_توسعه-yellow)
![نسخه](https://img.shields.io/badge/نسخه-1.0-blue)
![زبان](https://img.shields.io/badge/زبان-فارسی-green)

---

## 📖 معرفی

**رستوران‌یار** یک سامانهٔ تحت وب یکپارچه برای مدیریت رستوران‌های کوچک و متوسط است که این قابلیت‌ها را پوشش می‌دهد:

- 🍽 **ثبت سفارش مشتری** با QR روی میز یا موبایل
- 🪑 **مدیریت میز و رزرو**
- 👨‍🍳 **نمایشگر آشپزخانه (KDS)**
- 🛵 **مدیریت پیک و محاسبهٔ هزینهٔ ارسال**
- 📦 **کنترل موجودی انبار مواد اولیه**
- 💰 **محاسبهٔ حقوق کارمندان**
- 🎟 **سیستم کد تخفیف و باشگاه مشتریان**
- 📊 **گزارش‌گیری جامع**

---

## 🎯 هدف پروژه

جایگزینی روش‌های دستی (کاغذ و قلم) با یک سیستم یکپارچه و ساده که:

- ✅ خطاهای انسانی را کاهش می‌دهد
- ✅ سرعت خدمات را افزایش می‌دهد
- ✅ شفافیت مالی را فراهم می‌کند
- ✅ رضایت مشتری و کارکنان را بالا می‌برد

---

## 👥 تیم پروژه

| عضو | نقش |
|------|------|
| مبینا قرابیگلی | Backend Developer |
| آیلین کاوسیان | Backend + Data |
| یگانه کریم‌زاده | Frontend Developer |

---

## 🛠 فناوری‌ها

| لایه | فناوری |
|------|--------|
| **Frontend** | React 18 + Vite + Tailwind CSS + Redux Toolkit |
| **Backend** | Python 3.12 + FastAPI |
| **Database** | PostgreSQL 16 |
| **ORM** | SQLAlchemy + Alembic |
| **Auth** | python-jose (JWT) + passlib[bcrypt] |
| **Real-time** | python-socketio |
| **Testing** | pytest + httpx |
| **CI/CD** | GitHub Actions |
| **Deploy** | Railway |

---

## 📚 مستندات پروژه

همهٔ مستندات در پوشهٔ [`template/`](./template) قرار دارند:

| # | سند | فایل | توضیح |
|---|------|------|-------|
| ۱ | چشم‌انداز | [vision.md](./template/vision.md) | بیانیهٔ Vision، مشکل، فرصت، اهداف |
| ۲ | پرسوناها | [personas.md](./template/personas.md) | ۵ پرسونای کاربران هدف |
| ۳ | داستان‌های کاربر | [user-stories.md](./template/user-stories.md) | ۲۸ User Story با معیار پذیرش |
| ۴ | معماری | [architecture.md](./template/architecture.md) | معماری لایه‌ای، ADRها، APIها |
| ۵ | طرح دیتابیس | [database.md](./template/database.md) | ۱۹ جدول، روابط، ایندکس‌ها |
| ۶ | Use Case Diagram | [use-case.md](./template/use-case.md) | ۲۵ Use Case، ۹ Actor |
| ۷ | Use Case Specification | [use-case-specification.md](./template/use-case-specification.md) | مشخصات تفصیلی ۲۵ Use Case |
| ۸ | Class Diagram | [class-diagram.md](./template/class-diagram.md) | ۱۹ کلاس با روابط |
| ۹ | Class Specification | [class-specification.md](./template/class-specification.md) | مشخصات تفصیلی ۱۹ کلاس |
| ۱۰ | Sequence Diagram | [sequence-diagram.md](./template/sequence-diagram.md) | ۵ سناریوی تعامل |

---

## 🏗 ساختار پروژه

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
│   ├── alembic/                # Database Migrations
│   └── requirements.txt
│
├── frontend/                   # React
│   └── src/
│       ├── pages/
│       ├── components/
│       ├── services/
│       └── store/
│
├── template/                   # اسناد پروژه
│   ├── vision.md
│   ├── personas.md
│   ├── user-stories.md
│   ├── architecture.md
│   ├── database.md
│   ├── use-case.md
│   ├── use-case-specification.md
│   ├── class-diagram.md
│   ├── class-specification.md
│   └── sequence-diagram.md
│
└── README.md
```

---

## 🚀 راه‌اندازی (برای توسعه‌دهندگان)

### پیش‌نیازها

- Python 3.12+
- Node.js 20+
- PostgreSQL 16+

### نصب و راه‌اندازی

```bash
# 1. Clone
git clone https://github.com/USERNAME/restaurant-yar.git
cd restaurant-yar

# 2. Backend
cd backend
python -m venv venv
source venv/bin/activate  # ویندوز: venv\Scripts\activate
pip install -r requirements.txt

# 3. تنظیمات
cp .env.example .env
# DATABASE_URL و JWT_SECRET را در .env تنظیم کن

# 4. Migration
alembic upgrade head

# 5. اجرا
uvicorn app.main:app --reload
# باز کن: http://localhost:8000/docs

# 6. Frontend (ترمینال جدید)
cd frontend
npm install
npm run dev
# باز کن: http://localhost:5173
```

---

## 📊 معیارهای موفقیت

| معیار | هدف |
|--------|------|
| زمان ثبت سفارش | < ۲ دقیقه |
| دقت محاسبهٔ حقوق | ۱۰۰٪ |
| دقت محاسبهٔ هزینه ارسال | ۱۰۰٪ |
| پوشش تست | > ۷۰٪ |
| باگ بحرانی | صفر |

---

## 🗓 نقشهٔ راه

| Sprint | بازه | تمرکز |
|--------|------|-------|
| **Sprint 1** | هفته ۱-۵ | منو، کاربران، میز، سفارش |
| **Sprint 2** | هفته ۶-۱۱ | KDS، پیک، انبار، تخفیف |
| **Sprint 3** | هفته ۱۲-۱۴ | حقوق، گزارش، Deploy |

---

## 🎓 پروژهٔ درسی

این پروژه برای درس **مهندسی نرم‌افزار** تهیه شده است.
