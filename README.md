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
### کارایی
زمانی که همه داده‌ها در یک پایگاه داده ذخیره می‌شوند، برخی عملیات ممکن است بیش از حد کند شوند چون مدل داده‌ها بهینه‌سازی نشده برای همه انواع استفاده است.

### مقیاس‌پذیری
مدیریت بزرگی از داده‌ها در یک سیستم واحد می‌تواند محدودکننده باشد، چرا که افزایش حجم داده‌ها مستلزم توسعه زیرساخت‌ها در همان اندازه است.

### گهداری و تکامل
با افزایش پیچیدگی داده‌ها، تغییر و به‌روزرسانی مدل‌های داده‌ای مونولیتیک می‌تواند دشوار و خطرناک باشد.

که برای بهتر شدن و کنترل بهتر بهتر از Micro Service ها استفاده کنیم و همه چیمون داخل یک پروژه نباشه


## 4. No Caching Antipattern

 به یک الگوی نامناسب در طراحی نرم‌افزار اشاره دارد که در آن داده‌هایی که می‌توانند به طور مؤثر کش شوند (ذخیره موقت شوند)، با هر درخواست مجدداً بارگیری یا محاسبه می‌شوند. این الگو به ویژه زمانی مشکل‌ساز است که داده‌ها کمتر تغییر می‌کنند یا درخواست‌ها به طور مکرر برای داده‌های تکراری اتفاق می‌افتد. نادیده گرفتن کشینگ می‌تواند منجر به استفاده ناکارآمد از منابع سرور، افزایش زمان پاسخگویی و فشار زیاد بر پایگاه داده یا سایر سیستم‌های ذخیره‌سازی شود

 که راه حل این مشکل استفاده از Redis یا ابزار ها دیگه برای Cache کردن اطلاعات هستش


## 5. Retry Storm antipattern

به یک الگوی طراحی نامناسب در برنامه‌نویسی اشاره دارد که در آن تلاش‌های مکرر برای انجام درخواست‌های ناموفق بدون هیچ‌گونه استراتژی هوشمندانه یا فواصل زمانی مناسب بین تلاش‌ها صورت می‌گیرد. این می‌تواند در موقعیت‌های مختلفی رخ دهد، مانند تلاش برای دسترسی به یک سرویس وب که موقتاً در دسترس نیست یا ارتباط با یک پایگاه داده که پاسخ نمی‌دهد. Retry Storm می‌تواند به خستگی منابع سرور، افزایش بار شبکه و در نهایت شکست سیستم منجر شود.

چرا Retry Storm مشکل‌ساز است؟
افزایش بار سرور: تلاش‌های مکرر و پی‌درپی می‌تواند منجر به افزایش بار غیرمعمول و غیرضروری بر سرویس‌ها شود، به خصوص اگر تعداد زیادی از کلاینت‌ها به طور همزمان تلاش کنند.
کاهش عملکرد: بار اضافی تولید شده توسط تلاش‌های مکرر می‌تواند کل سیستم را کند کند و به کاهش عملکرد کلی منجر شود.
مشکلات مقیاس‌پذیری: در شرایطی که سیستم باید تحت بار سنگین عمل کند، Retry Storm می‌تواند زیرساخت‌ها را تحت فشار قرار دهد و مقیاس‌پذیری سیستم را محدود کند.

راه‌حل‌های بهبود

### Exponential Backoff
این روش شامل افزایش تدریجی فاصله زمانی بین تلاش‌های مجدد است. برای مثال، اگر اولین تلاش ناموفق باشد، قبل از تلاش بعدی یک دقیقه صبر کنید، سپس دو دقیقه، و به همین ترتیب. این کمک می‌کند تا فشار زیادی به سیستم وارد نشود و به سیستم فرصت دهد تا مشکلات موجود را حل کند.

### Jitter
اضافه کردن یک مقدار تصادفی (Jitter) به فواصل زمانی تلاش‌های مجدد. این کار مانع از تلاش‌های همزمان تعداد زیادی از درخواست‌ها می‌شود و به پراکندگی بار درخواست‌ها در زمان‌های مختلف کمک می‌کند.

### Circuit Breaker
پیاده‌سازی الگوی Circuit Breaker به گونه‌ای که پس از تعداد خاصی از تلاش‌های ناموفق، سیستم به طور خودکار برای مدتی تعیین شده از تلاش‌های بیشتر جلوگیری می‌کند. این امر به کاهش فشار بر منابع سیستم کمک می‌کند.

### Queueing Mechanism
استفاده از مکانیزم‌های صف‌بندی برای مدیریت درخواست‌ها و اطمینان از اینکه درخواست‌ها به طور کنترل‌شده و با حفظ ترتیب اولویت پردازش می‌شوند.

### Monitoring and Alerts
مانیتورینگ دقیق سیستم برای شناسایی سریع مشکلات و اطلاع‌رسانی به مدیران سیستم در صورت وقوع Retry Storm. این کمک می‌کند تا اقدامات به‌موقع برای کاهش تأثیر منفی این مشکلات انجام شود.



## 6. Chatty I/O Antipattern
به الگویی در برنامه‌نویسی اشاره دارد که در آن یک برنامه به طور مکرر و بیش از حد با سیستم‌های خارجی مانند پایگاه‌های داده یا سرویس‌های وب ارتباط برقرار می‌کند، و در نتیجه باعث می‌شود تعداد درخواست‌ها و پاسخ‌ها بین کلاینت و سرور به شدت افزایش یابد. این مشکل معمولاً منجر به کاهش عملکرد و زمان پاسخگویی طولانی‌تر می‌شود، چرا که هر درخواست شبکه ممکن است دارای تأخیر شبکه باشد و منابع سرور را درگیر کند.

مشکلات ناشی از Chatty I/O
کاهش کارایی: بارگذاری شدید بر شبکه و تأخیرات متعدد می‌تواند کارایی کلی سیستم را کاهش دهد.
بهره‌وری پایین سرور: سرورها باید بار بیشتری از درخواست‌ها را پردازش کنند، که می‌تواند منجر به استفاده ناکارآمد از CPU و حافظه شود.
تجربه کاربری ضعیف: تجربه کاربری می‌تواند به دلیل زمان بارگذاری بالا و پاسخگویی کند تحت تأثیر قرار گیرد.
```javascript
function fetchCustomerData(customerId) {
    fetch(`/api/customers/${customerId}/contact`).then(response => response.json()).then(contact => {
        console.log(contact);
    });
    fetch(`/api/customers/${customerId}/orders`).then(response => response.json()).then(orders => {
        console.log(orders);
    });
    fetch(`/api/customers/${customerId}/recommendations`).then(response => response.json()).then(recommendations => {
        console.log(recommendations);
    });
}
```

خب بیایم یک کد ساده بهبود یافته شو ببینیم

```javascript
async function fetchCustomerData(customerId) {
    try {
        const response = await fetch(`/api/customers/${customerId}/fullProfile`);
        const customerData = await response.json();
        console.log(customerData);
    } catch (error) {
        console.error('Failed to fetch customer data:', error);
    }
}
```

## 7.Synchronous I/O Antipattern

یک الگوی طراحی نامناسب در توسعه نرم‌افزار است که در آن برنامه‌ها منتظر پایان یک عملیات ورودی/خروجی (I/O) می‌مانند قبل از اینکه بتوانند ادامه دهند. این شیوه معمولاً منجر به بلاک شدن رشته‌ها یا پردازش‌هایی می‌شود که می‌توانند بدون انتظار برای تکمیل عملیات I/O فعالیت‌های دیگری انجام دهند. در محیط‌هایی که به عملکرد بالا نیاز است یا محیط‌هایی که منابع محدود هستند، استفاده از I/O همزمان می‌تواند به شدت مضر باشد و منجر به تجربیات کاربری ضعیف و کارایی پایین شود.

مشکلات ناشی از Synchronous I/O
بلاک شدن عملیات: هنگامی که پردازش برای تکمیل یک عملیات I/O بلاک می‌شود، منابعی مانند CPU و حافظه ممکن است زیر استفاده بمانند، چرا که برنامه قادر به انجام دیگر فعالیت‌ها نیست.
کاهش پاسخگویی: در برنامه‌هایی که به پاسخگویی بالا نیاز دارند، مانند برنامه‌های تعاملی یا برنامه‌های وب، بلاک شدن عملیات I/O می‌تواند باعث تأخیر در پاسخگویی به کاربران شود.
مشکلات مقیاس‌پذیری: در محیط‌هایی که باید درخواست‌های متعدد را به طور همزمان پردازش کنند، مدل I/O همزمان محدودیت‌های قابل توجهی ایجاد می‌کند.
```javascript
const fs = require('fs');

function readFileSync() {
    console.log('Starting synchronous file read.');
    const data = fs.readFileSync('file.txt', 'utf8');
    console.log(data);
    console.log('Finished synchronous file read.');
}

readFileSync();
```

و یک کد بهبود یافته ازشم ببینیم
```javascript
const fs = require('fs');

function readFileAsync() {
    console.log('Starting asynchronous file read.');
    fs.readFile('file.txt', 'utf8', (err, data) => {
        if (err) {
            console.error('Error reading file:', err);
            return;
        }
        console.log(data);
        console.log('Finished asynchronous file read.');
    });
}

readFileAsync();
```
