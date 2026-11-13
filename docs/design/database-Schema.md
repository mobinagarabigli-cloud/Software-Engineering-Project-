# سند طرح دیتابیس — رستوران‌یار

| | |
|---|---|
| **نام پروژه** | رستوران‌یار (RestaurantYar) |
| **نسخهٔ سند** | ۱.۰ |
| **تاریخ** | ۱۴۰۵/۰۷/۲۷ |
| **تهیه‌کنندگان** | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |
| **وضعیت** | پیش‌نویس |

---

## ۱. مقدمه

### ۱.۱ هدف سند

این سند طرح دیتابیس پروژهٔ **رستوران‌یار** را تعریف می‌کند. شامل جداول، روابط، ایندکس‌ها، نرمال‌سازی و تصمیم‌های طراحی.

### ۱.۲ دامنه

- **در دامنه:** ساختار جداول، روابط، ایندکس‌ها، نرمال‌سازی، امنیت داده.
- **خارج از دامنه:** Queryهای بهینه‌سازی‌شده، Performance Tuning سطح پایین.

---

## ۲. نمای کلی

### ۲.۱ DBMS انتخاب‌شده

**PostgreSQL 16**

**دلیل:**

- Relational و قدرتمند
- پشتیبانی از JSON برای تنظیمات پویا (مثل فرمول غذا و قوانین تخفیف)
- پشتیبانی از تراکنش برای عملیات مالی
- رایگان و اوپن‌سورس
- جامعهٔ بزرگ و مستندات کامل

### ۲.۲ ERD — نمای کلی

┌─────────────┐       ┌─────────────┐
│    users    │       │  customers  │
│ (همه نقش‌ها)│       │  (وفاداری)  │
└──────┬──────┘       └──────┬──────┘
       │                     │
       │ 1:N                 │ 1:N
       ▼                     ▼
┌─────────────┐       ┌─────────────┐
│  employees  │       │   orders    │
└─────────────┘       └──────┬──────┘
                             │ 1:N
                             ▼
┌─────────────┐       ┌─────────────┐
│    menu_    │──1:N──│ order_items │
│  categories │       └──────┬──────┘
└──────┬──────┘              │
       │ 1:N                 │ N:1
       ▼                     ▼
┌─────────────┐       ┌─────────────┐
│  menu_items │──N:M──│ ingredients │
└─────────────┘       └─────────────┘

---

## ۳. جداول

### ۳.۱ جدول users (همهٔ کاربران)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| email | VARCHAR(255) UNIQUE | ایمیل |
| password_hash | VARCHAR(255) | رمزنگاری‌شده با bcrypt |
| name | VARCHAR(100) | نام |
| phone | VARCHAR(20) | تلفن |
| role | VARCHAR(20) | admin / customer / waiter / chef / cashier / warehouse / delivery |
| is_active | BOOLEAN | فعال یا غیرفعال |
| created_at | TIMESTAMP | تاریخ ایجاد |

### ۳.۲ جدول customers (اطلاعات تکمیلی مشتریان)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| user_id | INTEGER FK | کاربر |
| address | TEXT | آدرس پیش‌فرض |
| loyalty_points | INTEGER | امتیاز وفاداری |
| wallet_balance | DECIMAL(12,0) | اعتبار کیف پول |
| created_at | TIMESTAMP | تاریخ ایجاد |

### ۳.۳ جدول menu_categories (دسته‌بندی منو)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| name | VARCHAR(100) | نام دسته |
| slug | VARCHAR(100) UNIQUE | شناسهٔ URL |
| parent_id | INTEGER | دستهٔ والد (اختیاری) |
| display_order | INTEGER | ترتیب نمایش |

### ۳.۴ جدول menu_items (غذاها و نوشیدنی‌ها)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| name | VARCHAR(255) | نام غذا |
| description | TEXT | توضیحات |
| price | DECIMAL(12,0) | قیمت (ریال) |
| cost_price | DECIMAL(12,0) | بهای تمام‌شده |
| category_id | INTEGER FK | دسته‌بندی |
| image_url | VARCHAR(500) | تصویر |
| prep_time_min | INTEGER | زمان آماده‌سازی (دقیقه) |
| is_available | BOOLEAN | موجود در منو |
| is_vegan | BOOLEAN | وگان |
| is_gluten_free | BOOLEAN | بدون گلوتن |
| created_at | TIMESTAMP | تاریخ ایجاد |

### ۳.۵ جدول ingredients (مواد اولیهٔ انبار)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| name | VARCHAR(150) | نام ماده |
| unit | VARCHAR(20) | کیلوگرم / لیتر / عدد |
| current_stock | DECIMAL(10,3) | موجودی فعلی |
| min_stock | DECIMAL(10,3) | حد بحرانی |
| unit_cost | DECIMAL(12,0) | قیمت واحد |
| supplier_id | INTEGER FK | تأمین‌کننده |
| expiry_date | DATE | تاریخ انقضا |

### ۳.۶ جدول menu_item_ingredients (فرمول غذا)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| menu_item_id | INTEGER FK | غذا |
| ingredient_id | INTEGER FK | ماده اولیه |
| quantity | DECIMAL(10,3) | مقدار مصرف |

### ۳.۷ جدول suppliers (تأمین‌کنندگان)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| name | VARCHAR(150) | نام تأمین‌کننده |
| phone | VARCHAR(20) | تلفن |
| address | TEXT | آدرس |
| created_at | TIMESTAMP | تاریخ ایجاد |

### ۳.۸ جدول tables (میزها)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| table_number | INTEGER UNIQUE | شمارهٔ میز |
| capacity | INTEGER | ظرفیت |
| status | VARCHAR(20) | free / occupied / reserved |
| qr_code | VARCHAR(255) | کد QR اختصاصی |

### ۳.۹ جدول reservations (رزرو میز)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| customer_id | INTEGER FK | مشتری |
| table_id | INTEGER FK | میز |
| reserved_at | TIMESTAMP | زمان رزرو |
| duration_min | INTEGER | مدت رزرو |
| status | VARCHAR(20) | pending / confirmed / cancelled |

### ۳.۱۰ جدول orders (سفارش‌ها)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| customer_id | INTEGER FK | مشتری |
| table_id | INTEGER FK | میز (اگر حضوری) |
| waiter_id | INTEGER FK | گارسون (اگر حضوری) |
| order_type | VARCHAR(20) | dine_in / takeaway / delivery |
| status | VARCHAR(30) | pending / preparing / ready / delivering / delivered / cancelled |
| subtotal | DECIMAL(12,0) | جمع غذاها |
| discount_amount | DECIMAL(12,0) | مقدار تخفیف |
| delivery_fee | DECIMAL(12,0) | هزینهٔ ارسال |
| tax_amount | DECIMAL(12,0) | مالیات |
| service_charge | DECIMAL(12,0) | حق سرویس |
| tip_amount | DECIMAL(12,0) | انعام |
| total | DECIMAL(12,0) | مبلغ نهایی |
| coupon_id | INTEGER FK | کد تخفیف استفاده‌شده |
| delivery_address | TEXT | آدرس (برای ارسال) |
| delivery_zone_id | INTEGER FK | منطقه (برای ارسال) |
| delivery_user_id | INTEGER FK | پیک تخصیص‌یافته |
| handed_to_driver_at | TIMESTAMP | زمان تحویل به پیک |
| created_at | TIMESTAMP | تاریخ ثبت |

### ۳.۱۱ جدول order_items (اقلام سفارش)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| order_id | INTEGER FK | سفارش |
| menu_item_id | INTEGER FK | غذا |
| quantity | INTEGER | تعداد |
| unit_price | DECIMAL(12,0) | قیمت واحد |
| customization | TEXT | شخصی‌سازی (بدون پیاز، اضافه پنیر) |
| extra_price | DECIMAL(12,0) | قیمت افزودنی‌ها |

### ۳.۱۲ جدول delivery_zones (مناطق ارسال)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| name | VARCHAR(100) | نام منطقه |
| fee | DECIMAL(12,0) | هزینهٔ ارسال |
| free_above | DECIMAL(12,0) | ارسال رایگان بالای این مبلغ |

### ۳.۱۳ جدول coupons (کدهای تخفیف)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| code | VARCHAR(50) UNIQUE | کد |
| type | VARCHAR(20) | percent / fixed |
| value | DECIMAL(12,0) | مقدار تخفیف |
| min_purchase | DECIMAL(12,0) | حداقل خرید |
| max_uses | INTEGER | سقف استفاده |
| used_count | INTEGER | تعداد استفاده‌شده |
| starts_at | TIMESTAMP | تاریخ شروع |
| expires_at | TIMESTAMP | تاریخ انقضا |
| apply_to_delivery | BOOLEAN | اعمال روی هزینه ارسال |

### ۳.۱۴ جدول payments (پرداخت‌ها)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| order_id | INTEGER FK | سفارش |
| method | VARCHAR(20) | cash / card / online |
| amount | DECIMAL(12,0) | مبلغ |
| status | VARCHAR(20) | success / failed / cancelled |
| paid_at | TIMESTAMP | تاریخ پرداخت |

### ۳.۱۵ جدول employees (اطلاعات تکمیلی کارمندان)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| user_id | INTEGER FK | کاربر |
| position | VARCHAR(50) | گارسون، آشپز، پیک، ... |
| base_salary | DECIMAL(12,0) | حقوق پایه |
| hire_date | DATE | تاریخ استخدام |
| is_active | BOOLEAN | فعال |

### ۳.۱۶ جدول shifts (شیفت‌های کاری)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| employee_id | INTEGER FK | کارمند |
| check_in | TIMESTAMP | زمان ورود |
| check_out | TIMESTAMP | زمان خروج |
| overtime_min | INTEGER | دقیقهٔ اضافه‌کاری |
| absence | BOOLEAN | غیبت |

### ۳.۱۷ جدول payrolls (فیش‌های حقوق)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| employee_id | INTEGER FK | کارمند |
| period | VARCHAR(7) | مثلاً 1405-07 |
| base_salary | DECIMAL(12,0) | حقوق پایه |
| overtime_amount | DECIMAL(12,0) | مبلغ اضافه‌کاری |
| bonus | DECIMAL(12,0) | پاداش |
| deduction | DECIMAL(12,0) | کسورات |
| net_amount | DECIMAL(12,0) | حقوق خالص |
| calculated_at | TIMESTAMP | تاریخ محاسبه |

### ۳.۱۸ جدول reviews (نظرات و امتیاز)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| customer_id | INTEGER FK | مشتری |
| menu_item_id | INTEGER FK | غذا |
| rating | INTEGER | ۱ تا ۵ |
| comment | TEXT | متن نظر |
| is_approved | BOOLEAN | تأیید مدیر |
| created_at | TIMESTAMP | تاریخ |

### ۳.۱۹ جدول notifications (اعلان‌ها)

| ستون | نوع | توضیح |
|------|------|-------|
| id | SERIAL PRIMARY KEY | شناسه |
| user_id | INTEGER FK | گیرنده |
| type | VARCHAR(30) | order / delivery / low_stock / shift |
| message | TEXT | متن |
| is_read | BOOLEAN | خوانده‌شده |
| created_at | TIMESTAMP | تاریخ |

---

## ۴. روابط

### ۴.۱ یک‌به‌چند (1:N)

| رابطه | توضیح |
|--------|-------|
| users → customers | هر کاربر می‌تواند یک رکورد مشتری داشته باشد |
| users → employees | هر کاربر می‌تواند یک رکورد کارمند داشته باشد |
| customers → orders | هر مشتری چند سفارش دارد |
| customers → reservations | هر مشتری چند رزرو دارد |
| menu_categories → menu_items | هر دسته چند غذا دارد |
| orders → order_items | هر سفارش چند قلم دارد |
| orders → payments | هر سفارش می‌تواند چند پرداخت داشته باشد |
| employees → shifts | هر کارمند چند شیفت دارد |
| employees → payrolls | هر کارمند چند فیش حقوقی دارد |
| suppliers → ingredients | هر تأمین‌کننده چند ماده اولیه دارد |
| delivery_zones → orders | هر منطقه چند سفارش دارد |
| coupons → orders | هر کد تخفیف روی چند سفارش اعمال می‌شود |
| tables → reservations | هر میز چند رزرو دارد |
| tables → orders | هر میز چند سفارش دارد |
| menu_items → reviews | هر غذا چند نظر دارد |

### ۴.۲ چند‌به‌چند (N:M)

| رابطه | جدول واسط | توضیح |
|--------|-----------|-------|
| orders ↔ menu_items | order_items | اقلام هر سفارش |
| menu_items ↔ ingredients | menu_item_ingredients | فرمول مصرف هر غذا |

---

## ۵. ایندکس‌ها

### ۵.۱ Primary Keys

همهٔ جداول: id

### ۵.۲ Foreign Keys

- menu_items.category_id → menu_categories.id
- menu_item_ingredients.menu_item_id → menu_items.id
- menu_item_ingredients.ingredient_id → ingredients.id
- orders.customer_id → customers.id
- orders.coupon_id → coupons.id
- order_items.menu_item_id → menu_items.id
- reviews.customer_id → customers.id
- payrolls.employee_id → employees.id

### ۵.۳ ایندکس‌های اضافی

| جدول | ستون | دلیل |
|------|------|------|
| users | email | ورود سریع |
| users | role | فیلتر بر اساس نقش |
| menu_items | name | جستجو |
| menu_items | category_id | فیلتر دسته |
| orders | customer_id | پیگیری سفارش مشتری |
| orders | status | گزارش و KDS |
| orders | created_at | گزارش فروش |
| coupons | code | اعتبارسنجی سریع |
| ingredients | current_stock | هشدار موجودی کم |
| notifications | user_id | نمایش اعلان‌ها |

---

## ۶. نرمال‌سازی

### ۶.۱ 1NF — حذف گروه‌های تکراری

- هر ستون یک مقدار اتمی دارد
- هیچ ستون چندمقداری نیست

### ۶.۲ 2NF — حذف وابستگی جزئی

- همهٔ ستون‌های غیرکلیدی به کل Primary Key وابسته‌اند
- در order_items، unit_price به menu_item_id وابسته است نه به کلید اصلی — اما چون قیمت در زمان سفارش قفل می‌شود، نگهداری آن در order_items درست است

### ۶.۳ 3NF — حذف وابستگی انتقالی

- هیچ ستون غیرکلیدی به ستون غیرکلیدی دیگر وابسته نیست
- menu_items.cost_price به‌صورت محاسبه‌شده از فرمول غذا ذخیره می‌شود (Denormalization آگاهانه برای سرعت گزارش سود)

**نتیجه:** دیتابیس تا 3NF نرمال است، با چند Denormalization آگاهانه برای سرعت گزارش‌گیری.

---

## ۷. امنیت داده

### ۷.۱ رمزنگاری

- گذرواژه‌ها با bcrypt (SALT_ROUNDS=12)
- ارتباط با DB با SSL
- داده‌های حساس (مثل شمارهٔ کارت) هرگز ذخیره نمی‌شوند

### ۷.۲ Backup

- روزانه به S3 یا فضای ابری
- نگهداری ۳۰ روز
- تست بازیابی ماهانه

### ۷.۳ دسترسی

- کاربر DB با حداقل دسترسی (Least Privilege)
- لاگ همهٔ Queryهای حساس
- دسترسی مستقیم به Production فقط برای Admin

---

## ۸. نمونهٔ Queryها

### ۸.۱ لیست منو یک دسته

SELECT m.id, m.name, m.price, m.is_available
FROM menu_items m
JOIN menu_categories c ON m.category_id = c.id
WHERE c.slug = 'main-dishes';

### ۸.۲ سفارش‌های یک مشتری

SELECT o.id, o.total, o.status, o.created_at
FROM orders o
WHERE o.customer_id = 5
ORDER BY o.created_at DESC;

### ۸.۳ گزارش فروش روزانه

SELECT DATE(created_at) AS date,
       COUNT(*) AS orders_count,
       SUM(total) AS revenue
FROM orders
WHERE status = 'delivered'
GROUP BY DATE(created_at)
ORDER BY date DESC;

### ۸.۴ مواد اولیهٔ کم‌موجود

SELECT id, name, current_stock, min_stock, unit
FROM ingredients
WHERE current_stock <= min_stock;

### ۸.۵ محاسبهٔ حقوق یک کارمند در یک ماه

SELECT e.id, u.name,
       p.base_salary,
       p.overtime_amount,
       p.bonus,
       p.deduction,
       p.net_amount
FROM payrolls p
JOIN employees e ON p.employee_id = e.id
JOIN users u ON e.user_id = u.id
WHERE p.period = '1405-07';

### ۸.۶ مواد اولیهٔ مصرفی یک سفارش

SELECT i.id, i.name, SUM(mii.quantity * oi.quantity) AS consumed
FROM order_items oi
JOIN menu_item_ingredients mii ON oi.menu_item_id = mii.menu_item_id
JOIN ingredients i ON mii.ingredient_id = i.id
WHERE oi.order_id = 1024
GROUP BY i.id, i.name;

---

## پیوست الف — تاریخچهٔ تغییرات

| نسخه | تاریخ | تغییرات | تهیه‌کنندگان |
|-------|-------|----------|--------------|
| ۱.۰ | ۱۴۰۵/۰۷/۲۷ | نسخهٔ اولیه | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |

---

## 📊 خلاصهٔ آمار سند

| مورد | تعداد |
|------|-------|
| جداول | ۱۹ |
| روابط 1:N | ۱۵ |
| روابط N:M | ۲ |
| ایندکس‌های اضافی | ۱۰ |
| Queryهای نمونه | ۶ |
