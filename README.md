# Antipatterns in Software Development

This README provides an overview of several common antipatterns in software development and recommended solutions to address them. Antipatterns are common practices that are initially thought to be beneficial but are counterproductive in the long run.



## 1. Extraneous Fetching

به الگویی در برنامه‌نویسی گفته می‌شود که در آن برنامه بیش از حد داده‌ها را از یک منبع، مانند پایگاه داده یا سرویس آنلاین، استخراج می‌کند. این کار معمولاً به منظور افزایش کارایی یا به دلیل نبود فیلترهای مناسب در درخواست‌ها انجام می‌شود. با این حال، در عمل، این الگو می‌تواند باعث کندی برنامه شود، چون مقدار زیادی داده بی‌نیاز باید از منبع داده به سمت کلاینت منتقل شود، که همین امر می‌تواند روی عملکرد شبکه و مصرف حافظه تأثیر منفی بگذارد.


```javascript
async function fetchAllUserData() {
    try {
        const response = await fetch('https://api.example.com/users');
        const users = await response.json();
        const userNames = users.map(user => user.name); // Only the names of the users are needed
        console.log(userNames);
    } catch (error) {
        console.error('Failed to fetch users:', error);
    }
}
```
خب بهتر شدش میشه اینشکلی
```javascript
async function fetchUserNamesOnly() {
    try {
        const response = await fetch('https://api.example.com/users?fields=name');
        const users = await response.json();
        console.log(users); // اکنون تنها نام‌های کاربران ارسال می‌شود
    } catch (error) {
        console.error('Failed to fetch user names:', error);
    }
}
```

## 2. Improper Instantiation antipattern

یک الگوی نامناسب در برنامه‌نویسی است که در آن اشیاء به طور نادرست یا بیش از حد ایجاد می‌شوند. این الگو معمولاً زمانی اتفاق می‌افتد که شیء‌هایی که باید تنها یک بار ساخته شوند، در هر بار استفاده مجدداً ساخته می‌شوند. این امر می‌تواند منجر به مصرف بیش از حد منابع حافظه و CPU شود، و عملکرد برنامه را کاهش دهد، به خصوص در محیط‌هایی که نیاز به سرعت و کارایی بالا دارند، مانند برنامه‌های تحت وب و موبایل.

```javascript
class DateFormatter {
    constructor(locale) {
        this.locale = locale;
    }

    formatDate(date) {
        return new Intl.DateTimeFormat(this.locale).format(date);
    }
}

function logDate(date) {
    const formatter = new DateFormatter('en-US'); // این ایجاد نمونه در هر فراخوانی می‌تواند منجر به استفاده زیاد از منابع شود
    console.log(formatter.formatDate(date));
}

logDate(new Date());
logDate(new Date());
logDate(new Date());
```
خب بهتر شدش میشه اینشکلی

```javascript
const formatter = new DateFormatter('en-US'); // ایجاد یک نمونه در سطح بالاتر

function logDate(date) {
    console.log(formatter.formatDate(date));
}

logDate(new Date());
logDate(new Date());
logDate(new Date());
```

## 3. Monolithic Persistence Antipattern

به یک الگوی نامناسب در طراحی نرم‌افزار و مدیریت داده‌ها اشاره دارد، جایی که تمام داده‌ها در یک پایگاه داده واحد، یا با استفاده از یک مدل داده‌ی یکپارچه ذخیره می‌شوند، بدون توجه به تفاوت‌های نیازها، مصرف داده، یا تناسب آن‌ها با مدل‌های ذخیره‌سازی متفاوت. این رویکرد می‌تواند به مشکلات متعددی منجر شود، از جمله کاهش کارایی، مشکلات در مقیاس‌پذیری و دشواری‌های در نگهداری و تکامل سیستم.

چرا Monolithic Persistence مشکل‌ساز است؟
کارایی: زمانی که همه داده‌ها در یک پایگاه داده ذخیره می‌شوند، برخی عملیات ممکن است بیش از حد کند شوند چون مدل داده‌ها بهینه‌سازی نشده برای همه انواع استفاده است.
مقیاس‌پذیری: مدیریت بزرگی از داده‌ها در یک سیستم واحد می‌تواند محدودکننده باشد، چرا که افزایش حجم داده‌ها مستلزم توسعه زیرساخت‌ها در همان اندازه است.
نگهداری و تکامل: با افزایش پیچیدگی داده‌ها، تغییر و به‌روزرسانی مدل‌های داده‌ای مونولیتیک می‌تواند دشوار و خطرناک باشد.

که برای بهتر شدن و کنترل بهتر بهتر از Micro Service ها استفاده کنیم و همه چیمون داخل یک پروژه نباشه


## 4. No Caching Antipattern

 به یک الگوی نامناسب در طراحی نرم‌افزار اشاره دارد که در آن داده‌هایی که می‌توانند به طور مؤثر کش شوند (ذخیره موقت شوند)، با هر درخواست مجدداً بارگیری یا محاسبه می‌شوند. این الگو به ویژه زمانی مشکل‌ساز است که داده‌ها کمتر تغییر می‌کنند یا درخواست‌ها به طور مکرر برای داده‌های تکراری اتفاق می‌افتد. نادیده گرفتن کشینگ می‌تواند منجر به استفاده ناکارآمد از منابع سرور، افزایش زمان پاسخگویی و فشار زیاد بر پایگاه داده یا سایر سیستم‌های ذخیره‌سازی شود

 که راه حل این مشکل استفاده از Redis یا ابزار ها دیگه برای Cache کردن اطلاعات هستش
