# Class Diagram — رستوران‌یار

| | |
|---|---|
| **نام پروژه** | رستوران‌یار (RestaurantYar) |
| **نسخهٔ سند** | ۱.۰ |
| **تاریخ** | ۱۴۰۵/۰۷/۲۸ |
| **تهیه‌کنندگان** | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |
| **وضعیت** | پیش‌نویس |

---

## ۱. مقدمه

این دیاگرام ساختار کلاس‌های اصلی سیستم رستوران‌یار و روابط بین آن‌ها را نشان می‌دهد. کلاس‌ها بر اساس موجودیت‌های اصلی دیتابیس و منطق کسب‌وکار رستوران طراحی شده‌اند.

---

## ۲. کلاس‌ها

### ۲.۱ User (کاربر پایه)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| name | string | نام |
| email | string | ایمیل |
| phone | string | تلفن |
| password_hash | string | رمزنگاری‌شده با bcrypt |
| role | enum | admin / customer / waiter / chef / cashier / warehouse / delivery |
| is_active | boolean | فعال یا غیرفعال |
| created_at | datetime | تاریخ ایجاد |

**متدها:**

- `login(email, password): Token`
- `changePassword(old, new): boolean`
- `hasPermission(action): boolean`

---

### ۲.۲ Customer (مشتری)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| user_id | int | کاربر مرتبط |
| address | string | آدرس پیش‌فرض |
| loyalty_points | int | امتیاز وفاداری |
| wallet_balance | decimal | اعتبار کیف پول |

**متدها:**

- `addPoints(amount): void`
- `usePoints(points): decimal`
- `getOrderHistory(): Order[]`

---

### ۲.۳ Employee (کارمند)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| user_id | int | کاربر مرتبط |
| position | string | گارسون، آشپز، پیک، ... |
| base_salary | decimal | حقوق پایه |
| hire_date | date | تاریخ استخدام |
| is_active | boolean | فعال |

**متدها:**

- `getShifts(): Shift[]`
- `calculateSalary(period): Payroll`

---

### ۲.۴ MenuCategory (دسته‌بندی منو)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| name | string | نام دسته |
| slug | string | شناسهٔ URL |
| parent_id | int | دستهٔ والد (اختیاری) |
| display_order | int | ترتیب نمایش |

**متدها:**

- `getItems(): MenuItem[]`

---

### ۲.۵ MenuItem (غذا)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| name | string | نام غذا |
| description | string | توضیحات |
| price | decimal | قیمت |
| cost_price | decimal | بهای تمام‌شده |
| category_id | int | دسته‌بندی |
| image_url | string | تصویر |
| prep_time_min | int | زمان آماده‌سازی |
| is_available | boolean | موجود در منو |
| is_vegan | boolean | وگان |
| is_gluten_free | boolean | بدون گلوتن |

**متدها:**

- `calculateProfit(): decimal`
- `checkAvailability(): boolean`
- `getIngredients(): Ingredient[]`

---

### ۲.۶ Ingredient (مواد اولیه)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| name | string | نام ماده |
| unit | string | کیلوگرم / لیتر / عدد |
| current_stock | decimal | موجودی فعلی |
| min_stock | decimal | حد بحرانی |
| unit_cost | decimal | قیمت واحد |
| supplier_id | int | تأمین‌کننده |
| expiry_date | date | تاریخ انقضا |

**متدها:**

- `decreaseStock(qty): void`
- `isLow(): boolean`
- `isExpired(): boolean`

---

### ۲.۷ MenuItemIngredient (فرمول غذا)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| menu_item_id | int | غذا |
| ingredient_id | int | ماده اولیه |
| quantity | decimal | مقدار مصرف |

**متدها:**

- `calculateConsumption(quantity): decimal`

---

### ۲.۸ Supplier (تأمین‌کننده)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| name | string | نام |
| phone | string | تلفن |
| address | string | آدرس |

**متدها:**

- `getIngredients(): Ingredient[]`

---

### ۲.۹ Table (میز)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| table_number | int | شمارهٔ میز |
| capacity | int | ظرفیت |
| status | enum | free / occupied / reserved |
| qr_code | string | کد QR |

**متدها:**

- `reserve(customer): Reservation`
- `occupy(): void`
- `free(): void`

---

### ۲.۱۰ Reservation (رزرو)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| customer_id | int | مشتری |
| table_id | int | میز |
| reserved_at | datetime | زمان رزرو |
| duration_min | int | مدت |
| status | enum | pending / confirmed / cancelled |

**متدها:**

- `confirm(): void`
- `cancel(): void`

---

### ۲.۱۱ Order (سفارش)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| customer_id | int | مشتری |
| table_id | int | میز |
| waiter_id | int | گارسون |
| order_type | enum | dine_in / takeaway / delivery |
| status | enum | pending / preparing / ready / delivering / delivered / cancelled |
| subtotal | decimal | جمع غذاها |
| discount_amount | decimal | تخفیف |
| delivery_fee | decimal | هزینهٔ ارسال |
| tax_amount | decimal | مالیات |
| service_charge | decimal | حق سرویس |
| tip_amount | decimal | انعام |
| total | decimal | مبلغ نهایی |
| coupon_id | int | کد تخفیف |
| delivery_address | string | آدرس |
| delivery_zone_id | int | منطقه |
| delivery_user_id | int | پیک |
| handed_to_driver_at | datetime | زمان تحویل به پیک |
| created_at | datetime | تاریخ ثبت |

**متدها:**

- `addItem(item): void`
- `removeItem(itemId): void`
- `calculateSubtotal(): decimal`
- `applyCoupon(code): decimal`
- `calculateDeliveryFee(zone): decimal`
- `calculateTax(): decimal`
- `calculateTotal(): decimal`
- `changeStatus(newStatus): void`
- `assignToDelivery(driver): void`
- `handToDriver(driverId): void`
- `cancel(reason): void`

---

### ۲.۱۲ OrderItem (قلم سفارش)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| order_id | int | سفارش |
| menu_item_id | int | غذا |
| quantity | int | تعداد |
| unit_price | decimal | قیمت واحد |
| customization | string | شخصی‌سازی |
| extra_price | decimal | قیمت افزودنی |

**متدها:**

- `getTotalPrice(): decimal`

---

### ۲.۱۳ DeliveryZone (منطقهٔ ارسال)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| name | string | نام منطقه |
| fee | decimal | هزینهٔ ارسال |
| free_above | decimal | ارسال رایگان بالای این مبلغ |

**متدها:**

- `calculateFee(orderAmount): decimal`

---

### ۲.۱۴ Coupon (کد تخفیف)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| code | string | کد |
| type | enum | percent / fixed |
| value | decimal | مقدار |
| min_purchase | decimal | حداقل خرید |
| max_uses | int | سقف استفاده |
| used_count | int | تعداد استفاده‌شده |
| starts_at | datetime | شروع |
| expires_at | datetime | انقضا |
| apply_to_delivery | boolean | اعمال روی ارسال |

**متدها:**

- `isValid(orderAmount): boolean`
- `calculateDiscount(amount): decimal`
- `markUsed(): void`

---

### ۲.۱۵ Payment (پرداخت)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| order_id | int | سفارش |
| method | enum | cash / card / online |
| amount | decimal | مبلغ |
| status | enum | success / failed / cancelled |
| paid_at | datetime | تاریخ پرداخت |

**متدها:**

- `process(): boolean`
- `refund(): boolean`

---

### ۲.۱۶ Shift (شیفت)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| employee_id | int | کارمند |
| check_in | datetime | ورود |
| check_out | datetime | خروج |
| overtime_min | int | دقیقهٔ اضافه‌کاری |
| absence | boolean | غیبت |

**متدها:**

- `calculateOvertime(): int`

---

### ۲.۱۷ Payroll (فیش حقوقی)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| employee_id | int | کارمند |
| period | string | مثلاً 1405-07 |
| base_salary | decimal | حقوق پایه |
| overtime_amount | decimal | اضافه‌کاری |
| bonus | decimal | پاداش |
| deduction | decimal | کسورات |
| net_amount | decimal | خالص |

**متدها:**

- `calculateNet(): decimal`
- `generatePayslip(): PDF`

---

### ۲.۱۸ Review (نظر)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| customer_id | int | مشتری |
| menu_item_id | int | غذا |
| rating | int | ۱ تا ۵ |
| comment | string | متن |
| is_approved | boolean | تأیید مدیر |

**متدها:**

- `approve(): void`
- `reject(): void`

---

### ۲.۱۹ Notification (اعلان)

| ویژگی | نوع | توضیح |
|--------|------|-------|
| id | int | شناسه |
| user_id | int | گیرنده |
| type | enum | order / delivery / low_stock / shift |
| message | string | متن |
| is_read | boolean | خوانده‌شده |

**متدها:**

- `markAsRead(): void`
- `send(): void`

---

## ۳. روابط

### ۳.۱ یک‌به‌یک (1:1)

| رابطه | توضیح |
|--------|-------|
| User → Customer | هر کاربر یک رکورد مشتری دارد |
| User → Employee | هر کاربر یک رکورد کارمند دارد |

### ۳.۲ یک‌به‌چند (1:N)

| رابطه | توضیح |
|--------|-------|
| Customer → Order | هر مشتری چند سفارش دارد |
| Customer → Reservation | هر مشتری چند رزرو دارد |
| MenuCategory → MenuItem | هر دسته چند غذا دارد |
| Supplier → Ingredient | هر تأمین‌کننده چند ماده |
| Table → Reservation | هر میز چند رزرو |
| Table → Order | هر میز چند سفارش |
| Order → OrderItem | هر سفارش چند قلم |
| Order → Payment | هر سفارش چند پرداخت |
| Employee → Shift | هر کارمند چند شیفت |
| Employee → Payroll | هر کارمند چند فیش حقوقی |
| MenuItem → Review | هر غذا چند نظر |
| User → Notification | هر کاربر چند اعلان |

### ۳.۳ چند‌به‌چند (N:M)

| رابطه | جدول واسط | توضیح |
|--------|-----------|-------|
| MenuItem ↔ Ingredient | MenuItemIngredient | فرمول مصرف هر غذا |

### ۳.۴ ارتباط چند‌به‌یک (N:1)

| رابطه | توضیح |
|--------|-------|
| Order → DeliveryZone | هر سفارش یک منطقه |
| Order → Coupon | هر سفارش یک کد تخفیف |
| Order → User (delivery) | هر سفارش یک پیک |

---

## ۴. دیاگرام کلاس‌ها (نمای کلی)

```
                    ┌──────────┐
                    │   User   │
                    └────┬─────┘
                         │
          ┌──────────────┼──────────────┐
          │              │              │
    ┌─────▼─────┐  ┌─────▼─────┐  ┌─────▼─────┐
    │ Customer  │  │ Employee  │  │DeliveryUser│
    └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
          │              │              │
          │         ┌────▼────┐         │
          │         │  Shift  │         │
          │         └─────────┘         │
          │         ┌─────────┐         │
          │         │ Payroll │         │
          │         └─────────┘         │
          │                             │
          ▼                             │
    ┌──────────┐                        │
    │  Order   │◄───────────────────────┘
    └────┬─────┘
         │
    ┌────┴─────┬──────────┬──────────┐
    │          │          │          │
    ▼          ▼          ▼          ▼
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│OrderItem│ │Payment │ │ Coupon │ │Delivery│
└────┬───┘ └────────┘ └────────┘ │  Zone  │
     │                            └────────┘
     ▼
┌──────────┐        ┌─────────────┐
│ MenuItem │◄───────│MenuCategory │
└────┬─────┘        └─────────────┘
     │
     ▼
┌──────────────────┐        ┌────────────┐
│MenuItemIngredient│◄──────►│ Ingredient │
└──────────────────┘        └─────┬──────┘
                                  │
                            ┌─────▼─────┐
                            │ Supplier  │
                            └───────────┘

┌──────────┐        ┌─────────────┐
│  Table   │───────►│ Reservation │
└────┬─────┘        └─────────────┘
     │
     └──────────────► Order

┌──────────┐
│  Review  │◄──── MenuItem, Customer
└──────────┘

┌──────────────┐
│ Notification │◄──── User
└──────────────┘
```

---

## ۵. نکات طراحی

### ۵.۱ استفاده از Enum

برای فیلدهای محدود (role، status، order_type، method) از enum استفاده شده تا اعتبارسنجی در سطح دیتابیس انجام شود.

### ۵.۲ جداسازی User از Customer و Employee

به‌جای قرار دادن همهٔ فیلدها در یک جدول بزرگ، اطلاعات پایه در User و اطلاعات تخصصی در Customer و Employee نگهداری می‌شود (ارث‌بری در سطح جدول).

### ۵.۳ محاسبهٔ هزینه در سمت سرور

همهٔ متدهای محاسباتی (`calculateTotal`، `calculateTax`، `calculateDeliveryFee`) در کلاس Order قرار دارند تا از دستکاری سمت کلاینت جلوگیری شود.

### ۵.۴ جدول واسط برای N:M

رابطهٔ چندبه‌چند بین MenuItem و Ingredient با جدول واسط MenuItemIngredient پیاده‌سازی شده تا هم فرمول غذا و هم مقدار مصرف قابل ذخیره باشد.

### ۵.۵ اعلان‌ها

کلاس Notification برای همهٔ انواع اعلان‌ها (سفارش، ارسال، موجودی کم، شیفت) استفاده می‌شود و نوع آن با enum مشخص می‌شود.

### ۵.۶ نقش گستردهٔ گارسون

کلاس Order متد `handToDriver()` دارد که توسط گارسون برای تحویل سفارش آنلاین به پیک فراخوانی می‌شود و زمان تحویل را در فیلد `handed_to_driver_at` ثبت می‌کند.

---

## پیوست الف — تاریخچهٔ تغییرات

| نسخه | تاریخ | تغییرات | تهیه‌کنندگان |
|-------|-------|----------|--------------|
| ۱.۰ | ۱۴۰۵/۰۷/۲۸ | نسخهٔ اولیه | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |

---

## 📊 خلاصهٔ آمار سند

| مورد | تعداد |
|------|-------|
| کلاس‌ها | ۱۹ |
| روابط 1:1 | ۲ |
| روابط 1:N | ۱۲ |
| روابط N:M | ۱ |
| متدها | ۴۰+ |
| ویژگی‌ها | ۱۰۰+ |
