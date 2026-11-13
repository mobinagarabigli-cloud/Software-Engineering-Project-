# Class Specification — رستوران‌یار

| | |
|---|---|
| **نام پروژه** | رستوران‌یار (RestaurantYar) |
| **نسخهٔ سند** | ۱.۰ |
| **تاریخ** | ۱۴۰۵/۰۷/۲۹ |
| **تهیه‌کنندگان** | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |
| **وضعیت** | پیش‌نویس |

---

## ۱. مقدمه

این سند مشخصات تفصیلی کلاس‌های اصلی سیستم رستوران‌یار را تعریف می‌کند. برای هر کلاس، مسئولیت، ویژگی‌ها، متدها، روابط و قواعد کسب‌وکار مشخص شده است.

---

## ۲. مشخصات کلاس‌ها

### ۲.۱ User (کاربر پایه)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | نگهداری اطلاعات پایهٔ همهٔ کاربران سیستم و مدیریت احراز هویت |
| **ویژگی‌ها** | id, name, email, phone, password_hash, role, is_active, created_at |
| **متدها** | login(email, password), changePassword(old, new), hasPermission(action) |
| **روابط** | User 1:1 Customer، User 1:1 Employee، User 1:N Notification |
| **پیش‌شرط** | email یکتا، password حداقل ۸ کاراکتر |
| **پس‌شرط** | پس از login موفق، JWT تولید می‌شود |

---

### ۲.۲ Customer (مشتری)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | مدیریت اطلاعات تکمیلی مشتری، امتیاز وفاداری و کیف پول |
| **ویژگی‌ها** | id, user_id, address, loyalty_points, wallet_balance |
| **متدها** | addPoints(amount), usePoints(points), getOrderHistory() |
| **روابط** | User 1:1 Customer، Customer 1:N Order، Customer 1:N Reservation، Customer 1:N Review |
| **پیش‌شرط** | user_id معتبر و موجود |
| **پس‌شرط** | پس از addPoints، loyalty_points افزایش می‌یابد |

---

### ۲.۳ Employee (کارمند)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | نگهداری اطلاعات شغلی کارمندان (گارسون، آشپز، صندوق‌دار، انباردار، پیک) |
| **ویژگی‌ها** | id, user_id, position, base_salary, hire_date, is_active |
| **متدها** | getShifts(), calculateSalary(period) |
| **روابط** | User 1:1 Employee، Employee 1:N Shift، Employee 1:N Payroll |
| **پیش‌شرط** | base_salary > 0 |
| **پس‌شرط** | پس از calculateSalary، Payroll تولید می‌شود |

---

### ۲.۴ MenuCategory (دسته‌بندی منو)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | گروه‌بندی غذاها و نوشیدنی‌ها (پیش‌غذا، غذای اصلی، دسر، نوشیدنی) |
| **ویژگی‌ها** | id, name, slug, parent_id, display_order |
| **متدها** | getItems() |
| **روابط** | MenuCategory 1:N MenuItem |
| **پیش‌شرط** | slug یکتا |
| **پس‌شرط** | پس از ذخیره، در منو نمایش داده می‌شود |

---

### ۲.۵ MenuItem (غذا)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | نمایش و مدیریت غذاها و نوشیدنی‌های منو |
| **ویژگی‌ها** | id, name, description, price, cost_price, category_id, image_url, prep_time_min, is_available, is_vegan, is_gluten_free |
| **متدها** | calculateProfit(), checkAvailability(), getIngredients() |
| **روابط** | MenuCategory 1:N MenuItem، MenuItem N:M Ingredient (از طریق MenuItemIngredient)، MenuItem 1:N Review |
| **پیش‌شرط** | price > 0، category_id معتبر |
| **پس‌شرط** | calculateProfit = price - cost_price |

---

### ۲.۶ Ingredient (مواد اولیه)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | مدیریت موجودی مواد اولیهٔ انبار و هشدار کمبود |
| **ویژگی‌ها** | id, name, unit, current_stock, min_stock, unit_cost, supplier_id, expiry_date |
| **متدها** | decreaseStock(qty), isLow(), isExpired() |
| **روابط** | Ingredient N:M MenuItem (از طریق MenuItemIngredient)، Supplier 1:N Ingredient |
| **پیش‌شرط** | current_stock >= 0 |
| **پس‌شرط** | پس از decreaseStock، current_stock کاهش می‌یابد |

---

### ۲.۷ MenuItemIngredient (فرمول غذا)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | نگهداری مقدار مصرف هر ماده اولیه در هر غذا |
| **ویژگی‌ها** | id, menu_item_id, ingredient_id, quantity |
| **متدها** | calculateConsumption(quantity) |
| **روابط** | MenuItem 1:N MenuItemIngredient، Ingredient 1:N MenuItemIngredient |
| **پیش‌شرط** | menu_item_id و ingredient_id معتبر |
| **پس‌شرط** | quantity > 0 |

---

### ۲.۸ Supplier (تأمین‌کننده)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | نگهداری اطلاعات تأمین‌کنندگان مواد اولیه |
| **ویژگی‌ها** | id, name, phone, address |
| **متدها** | getIngredients() |
| **روابط** | Supplier 1:N Ingredient |
| **پیش‌شرط** | name غیرخالی |
| **پس‌شرط** | پس از ذخیره، در لیست تأمین‌کنندگان نمایش داده می‌شود |

---

### ۲.۹ Table (میز)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | مدیریت وضعیت میزهای سالن و اتصال به QR |
| **ویژگی‌ها** | id, table_number, capacity, status, qr_code |
| **متدها** | reserve(customer), occupy(), free() |
| **روابط** | Table 1:N Reservation، Table 1:N Order |
| **پیش‌شرط** | table_number و qr_code یکتا |
| **پس‌شرط** | پس از occupy، status = 'occupied' |

---

### ۲.۱۰ Reservation (رزرو)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | مدیریت رزرو میز توسط مشتری |
| **ویژگی‌ها** | id, customer_id, table_id, reserved_at, duration_min, status |
| **متدها** | confirm(), cancel() |
| **روابط** | Customer 1:N Reservation، Table 1:N Reservation |
| **پیش‌شرط** | میز در آن بازه آزاد باشد |
| **پس‌شرط** | پس از confirm، status = 'confirmed' |

---

### ۲.۱۱ Order (سفارش)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | مدیریت کامل سفارش از ثبت تا تحویل، شامل محاسبهٔ هزینه‌ها و تغییر وضعیت |
| **ویژگی‌ها** | id, customer_id, table_id, waiter_id, order_type, status, subtotal, discount_amount, delivery_fee, tax_amount, service_charge, tip_amount, total, coupon_id, delivery_address, delivery_zone_id, delivery_user_id, handed_to_driver_at, created_at |
| **متدها** | addItem(item), removeItem(itemId), calculateSubtotal(), applyCoupon(code), calculateDeliveryFee(zone), calculateTax(), calculateTotal(), changeStatus(newStatus), assignToDelivery(driver), handToDriver(driverId), cancel(reason) |
| **روابط** | Customer 1:N Order، Table 1:N Order، Order 1:N OrderItem، Order 1:N Payment، Order N:1 Coupon، Order N:1 DeliveryZone، Order N:1 User (پیک) |
| **پیش‌شرط** | حداقل یک OrderItem برای ثبت نهایی |
| **پس‌شرط** | پس از ثبت، status = 'pending'، موجودی کاهش می‌یابد |

---

### ۲.۱۲ OrderItem (قلم سفارش)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | نمایش یک قلم از سفارش همراه با شخصی‌سازی |
| **ویژگی‌ها** | id, order_id, menu_item_id, quantity, unit_price, customization, extra_price |
| **متدها** | getTotalPrice() |
| **روابط** | Order 1:N OrderItem، MenuItem 1:N OrderItem |
| **پیش‌شرط** | quantity > 0 |
| **پس‌شرط** | getTotalPrice = (quantity × unit_price) + extra_price |

---

### ۲.۱۳ DeliveryZone (منطقهٔ ارسال)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | تعریف مناطق ارسال و هزینهٔ هر منطقه |
| **ویژگی‌ها** | id, name, fee, free_above |
| **متدها** | calculateFee(orderAmount) |
| **روابط** | DeliveryZone 1:N Order |
| **پیش‌شرط** | name غیرخالی، fee >= 0 |
| **پس‌شرط** | اگر orderAmount >= free_above، fee = 0 |

---

### ۲.۱۴ Coupon (کد تخفیف)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | تعریف و اعتبارسنجی کدهای تخفیف |
| **ویژگی‌ها** | id, code, type, value, min_purchase, max_uses, used_count, starts_at, expires_at, apply_to_delivery |
| **متدها** | isValid(orderAmount), calculateDiscount(amount), markUsed() |
| **روابط** | Coupon 1:N Order |
| **پیش‌شرط** | code یکتا |
| **پس‌شرط** | پس از markUsed، used_count افزایش می‌یابد |

---

### ۲.۱۵ Payment (پرداخت)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | ثبت و پیگیری تراکنش‌های مالی سفارش |
| **ویژگی‌ها** | id, order_id, method, amount, status, paid_at |
| **متدها** | process(), refund() |
| **روابط** | Order 1:N Payment |
| **پیش‌شرط** | order_id معتبر، amount > 0 |
| **پس‌شرط** | پس از process موفق، status = 'success' |

---

### ۲.۱۶ Shift (شیفت کاری)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | ثبت ساعت ورود و خروج و اضافه‌کاری کارمندان |
| **ویژگی‌ها** | id, employee_id, check_in, check_out, overtime_min, absence |
| **متدها** | calculateOvertime() |
| **روابط** | Employee 1:N Shift |
| **پیش‌شرط** | check_out > check_in |
| **پس‌شرط** | calculateOvertime بر اساس اختلاف زمان محاسبه می‌شود |

---

### ۲.۱۷ Payroll (فیش حقوقی)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | محاسبه و نگهداری فیش حقوقی ماهانهٔ کارمندان |
| **ویژگی‌ها** | id, employee_id, period, base_salary, overtime_amount, bonus, deduction, net_amount |
| **متدها** | calculateNet(), generatePayslip() |
| **روابط** | Employee 1:N Payroll |
| **پیش‌شرط** | period به فرمت YYYY-MM |
| **پس‌شرط** | net_amount = base_salary + overtime_amount + bonus - deduction |

---

### ۲.۱۸ Review (نظر و امتیاز)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | ثبت و تأیید نظرات و امتیازهای مشتری برای غذاها |
| **ویژگی‌ها** | id, customer_id, menu_item_id, rating, comment, is_approved |
| **متدها** | approve(), reject() |
| **روابط** | Customer 1:N Review، MenuItem 1:N Review |
| **پیش‌شرط** | rating بین ۱ تا ۵ |
| **پس‌شرط** | پس از approve، is_approved = true |

---

### ۲.۱۹ Notification (اعلان)

| بخش | توضیح |
|------|-------|
| **مسئولیت** | ارسال و مدیریت اعلان‌های درون‌سیستمی برای کاربران |
| **ویژگی‌ها** | id, user_id, type, message, is_read, created_at |
| **متدها** | markAsRead(), send() |
| **روابط** | User 1:N Notification |
| **پیش‌شرط** | user_id معتبر، type از enum مجاز |
| **پس‌شرط** | پس از markAsRead، is_read = true |

---

## ۳. جدول جمع‌بندی کلاس‌ها

| # | کلاس | مسئولیت اصلی | تعداد رابطه | تعداد متد |
|---|------|----------------|-------------|-----------|
| ۱ | User | احراز هویت و نقش‌ها | ۳ | ۳ |
| ۲ | Customer | اطلاعات مشتری و وفاداری | ۴ | ۳ |
| ۳ | Employee | اطلاعات کارمندان | ۳ | ۲ |
| ۴ | MenuCategory | دسته‌بندی منو | ۱ | ۱ |
| ۵ | MenuItem | غذاها و نوشیدنی‌ها | ۳ | ۳ |
| ۶ | Ingredient | مواد اولیهٔ انبار | ۲ | ۳ |
| ۷ | MenuItemIngredient | فرمول غذا | ۲ | ۱ |
| ۸ | Supplier | تأمین‌کنندگان | ۱ | ۱ |
| ۹ | Table | میزهای سالن | ۲ | ۳ |
| ۱۰ | Reservation | رزرو میز | ۲ | ۲ |
| ۱۱ | Order | مدیریت سفارش | ۷ | ۱۱ |
| ۱۲ | OrderItem | اقلام سفارش | ۲ | ۱ |
| ۱۳ | DeliveryZone | مناطق ارسال | ۱ | ۱ |
| ۱۴ | Coupon | کد تخفیف | ۱ | ۳ |
| ۱۵ | Payment | پرداخت‌ها | ۱ | ۲ |
| ۱۶ | Shift | شیفت کاری | ۱ | ۱ |
| ۱۷ | Payroll | فیش حقوقی | ۱ | ۲ |
| ۱۸ | Review | نظرات | ۲ | ۲ |
| ۱۹ | Notification | اعلان‌ها | ۱ | ۲ |

---

## ۴. نکات طراحی

### ۴.۱ جداسازی مسئولیت‌ها
هر کلاس فقط یک مسئولیت اصلی دارد. مثلاً محاسبهٔ هزینه‌ها فقط در Order انجام می‌شود، نه در OrderItem یا MenuItem.

### ۴.۲ محاسبه در سمت سرور
همهٔ متدهای محاسباتی (calculateTotal، calculateTax، calculateNet) در سمت سرور اجرا می‌شوند تا از دستکاری کلاینت جلوگیری شود.

### ۴.۳ نقش گستردهٔ گارسون
متد `handToDriver()` در کلاس Order، توسط گارسون برای تحویل سفارش آنلاین به پیک فراخوانی می‌شود و زمان تحویل را در فیلد `handed_to_driver_at` ثبت می‌کند.

### ۴.۴ امنیت رمز عبور
رمز عبور با bcrypt (SALT_ROUNDS=12) هش می‌شود و هرگز به‌صورت متن ساده ذخیره نمی‌شود.

---

## پیوست الف — تاریخچهٔ تغییرات

| نسخه | تاریخ | تغییرات | تهیه‌کنندگان |
|-------|-------|----------|--------------|
| ۱.۰ | ۱۴۰۵/۰۷/۲۹ | نسخهٔ اولیه | مبینا قرابیگلی، آیلین کاوسیان، یگانه کریم‌زاده |

---

## 📊 خلاصهٔ آمار سند

| مورد | تعداد |
|------|-------|
| کلاس‌های مستندشده | ۱۹ |
| متدها | ۵۰+ |
| روابط | ۳۰+ |
| پیش‌شرط و پس‌شرط | ۳۸ |
