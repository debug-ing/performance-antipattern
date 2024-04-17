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
خب بهتر شدش میشه این
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

