# Antipatterns in Software Development

This README provides an overview of several common antipatterns in software development and recommended solutions to address them. Antipatterns are common practices that are initially thought to be beneficial but are counterproductive in the long run.



## 1. Extraneous Fetching

به الگویی در برنامه‌نویسی گفته می‌شود که در آن برنامه بیش از حد داده‌ها را از یک منبع، مانند پایگاه داده یا سرویس آنلاین، استخراج می‌کند. این کار معمولاً به منظور افزایش کارایی یا به دلیل نبود فیلترهای مناسب در درخواست‌ها انجام می‌شود. با این حال، در عمل، این الگو می‌تواند باعث کندی برنامه شود، چون مقدار زیادی داده بی‌نیاز باید از منبع داده به سمت کلاینت منتقل شود، که همین امر می‌تواند روی عملکرد شبکه و مصرف حافظه تأثیر منفی بگذارد.
