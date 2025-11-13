# آموزش Git Flow

[English Tutorial](README.md)

این ریپازیتوری یک منبع یادگیری شخصی برای درک و تمرین Git Flow است، یک مدل شاخه‌بندی برای Git که توسط Vincent Driessen معرفی شده است. Git Flow یک روش ساختاریافته برای مدیریت توسعه ویژگی‌ها، انتشارها و هات‌فیکس‌ها در پروژه‌های نرم‌افزاری نسخه‌دار فراهم می‌کند. از مجموعه‌ای از افزونه‌های Git برای ساده‌سازی عملیات سطح بالا در ریپازیتوری استفاده می‌کند.

در حالی که Git Flow برای پروژه‌هایی با نسخه‌بندی صریح و چندین انتشار پشتیبانی‌شده محبوب بوده است، توجه کنید که برای تحویل مداوم مدرن (مانند اپ‌های وب)، جریان‌های کاری ساده‌تر مبتنی بر trunk اغلب به عنوان بهترین شیوه‌ها توصیه می‌شوند.

این آموزش بر روی Git Flow برای اهداف آموزشی تمرکز دارد و تمام دستورات ممکن، جریان‌های کاری و جزئیات را پوشش می‌دهد.

## Git Flow چیست؟

Git Flow یک استراتژی شاخه‌بندی است که انواع شاخه‌های خاص و قوانین برای ایجاد، ادغام و حذف آن‌ها را تعریف می‌کند. توسعه مداوم را از کد تولید پایدار جدا می‌کند.

### شاخه‌های کلیدی
- **master**: نمایانگر کد آماده تولید است. هر کامیت یک انتشار قابل استقرار است که با شماره نسخه تگ می‌شود.
- **develop**: شاخه ادغام برای انتشار بعدی. ویژگی‌ها اینجا ادغام می‌شوند.
- **شاخه‌های Feature**: برای ویژگی‌های جدید، از develop شاخه می‌شوند و به develop ادغام می‌شوند.
- **شاخه‌های Release**: برای آماده‌سازی انتشارها، از develop شاخه می‌شوند، فقط با اصلاحات جزئی.
- **شاخه‌های Hotfix**: برای اصلاحات فوری تولید، از master شاخه می‌شوند.

### خلاصه چرخه حیات شاخه‌ها

| نوع شاخه | ایجاد از | ادغام به | طول عمر | هدف |
|----------|----------|----------|---------|-----|
| master   | —        | —        | نامحدود | انتشارهای تولید |
| develop  | —        | —        | نامحدود | ادغام انتشار بعدی |
| Feature  | develop  | develop  | موقت    | ویژگی‌های جدید |
| Release  | develop  | master, develop | موقت    | آماده‌سازی انتشار (اصلاح باگ، متادیتا) |
| Hotfix   | master (یا تگ) | master, develop | موقت    | اصلاحات حیاتی تولید |

## نصب

Git Flow نیاز به نصب Git دارد. افزونه‌های `git-flow` را روی پلتفرم خود نصب کنید:

### macOS
- **Homebrew**: `brew install git-flow-avh`
- **Macports**: `port install git-flow-avh`

### Linux
- `apt-get install git-flow`

### Windows (Cygwin)
- دانلود و اجرای نصب‌کننده: `wget -q -O - --no-check-certificate https://raw.github.com/petervanderdoes/gitflow-avh/develop/contrib/gitflow-installer.sh install stable | bash`

برای نصب به wget و util-linux نیاز دارید.

## مقداردهی اولیه

Git Flow را در یک ریپازیتوری Git موجود مقداردهی اولیه کنید تا کنوانسیون‌های نام‌گذاری شاخه‌ها تنظیم شود (پیش‌فرض‌ها توصیه می‌شود):

```bash
git flow init
```

این شاخه‌هایی مانند `master`، `develop`، `feature/`، `release/`، `hotfix/` و `support/` را پیکربندی می‌کند. به پرامپت‌ها پاسخ دهید یا پیش‌فرض‌ها را بپذیرید.

## شاخه‌های Feature

شاخه‌های Feature کار توسعه جدید را ایزوله می‌کنند. عمدتاً در ریپوهای محلی وجود دارند.

### شروع یک Feature جدید
از `develop` شاخه ایجاد می‌کند و به آن سوئیچ می‌کند:

```bash
git flow feature start MYFEATURE
```

معادل Git ساده:
```bash
git checkout -b feature/MYFEATURE develop
```

### پایان یک Feature
به `develop` ادغام می‌کند، شاخه را حذف می‌کند و به عقب سوئیچ می‌کند:

```bash
git flow feature finish MYFEATURE
```

معادل Git ساده:
```bash
git checkout develop
git merge --no-ff feature/MYFEATURE
git branch -d feature/MYFEATURE
git push origin develop
```

از `--no-ff` برای ایجاد کامیت ادغام برای حفظ تاریخچه استفاده کنید.

### انتشار یک Feature (برای همکاری)
به ریموت پوش کنید:

```bash
git flow feature publish MYFEATURE
```

معادل: `git push origin feature/MYFEATURE`

### کشیدن یک Feature منتشرشده
از ریموت واکشی کنید:

```bash
git flow feature pull origin MYFEATURE
```

### ردیابی یک Feature ریموت
```bash
git flow feature track MYFEATURE
```

تمام ویژگی‌ها فقط باید به `develop` ادغام شوند — هیچ ادغام مستقیمی به `master` نیست.

## شاخه‌های Release

شاخه‌های Release یک نسخه تولید جدید را آماده می‌کنند، اجازه اصلاح باگ و به‌روزرسانی متادیتا (مانند افزایش نسخه) بدون ویژگی‌های جدید را می‌دهند.

### شروع یک Release
از `develop` شاخه می‌شود (اختیاری از کامیت خاص BASE):

```bash
git flow release start RELEASE [BASE]
```

معادل Git ساده:
```bash
git checkout -b release/RELEASE develop
# افزایش نسخه (مانند از طریق اسکریپت)
./bump-version.sh RELEASE
git commit -a -m "Bumped version to RELEASE"
```

برای اصلاحات تیمی منتشر کنید:
```bash
git flow release publish RELEASE
```

ردیابی یک release ریموت:
```bash
git flow release track RELEASE
```

### پایان یک Release
به `master` ادغام می‌کند (تگ‌شده)، به `develop` برگشت می‌دهد و شاخه را حذف می‌کند:

```bash
git flow release finish RELEASE
git push origin --tags  # پوش تگ‌ها
```

معادل Git ساده:
```bash
git checkout master
git merge --no-ff release/RELEASE
git tag -a RELEASE -m "Release RELEASE"  # از -s برای تگ‌های امضاشده استفاده کنید
git checkout develop
git merge --no-ff release/RELEASE
git branch -d release/RELEASE
```

هر تعارض ادغام (مانند شماره نسخه‌ها) را حل کنید.

## شاخه‌های Hotfix

Hotfixها مسائل حیاتی در تولید را سریعاً حل می‌کنند.

### شروع یک Hotfix
از `master` شاخه می‌شود (یا تگ، اختیاری از BASENAME):

```bash
git flow hotfix start VERSION [BASENAME]
```

معادل Git ساده:
```bash
git checkout -b hotfix/VERSION master
./bump-version.sh VERSION
git commit -a -m "Bumped version to VERSION"
```

### پایان یک Hotfix
به `master` ادغام می‌کند (تگ‌شده)، `develop` و شاخه را حذف می‌کند:

```bash
git flow hotfix finish VERSION
git push origin --tags
```

معادل Git ساده:
```bash
git checkout master
git merge --no-ff hotfix/VERSION
git tag -a VERSION -m "Hotfix VERSION"
git checkout develop
git merge --no-ff hotfix/VERSION
git branch -d hotfix/VERSION
```

اگر شاخه release وجود داشته باشد، hotfix را به جای `develop` به آن ادغام کنید.

## بهترین شیوه‌ها

- **کامیت اغلب، پوش یک‌بار**: کامیت‌های کوچک و متمرکز برای هر مسئله بسازید، سپس وقتی آماده شد پوش کنید.
- **پیام‌های کامیت توصیفی**: از پیام‌های واضح و مختصر استفاده کنید (مانند "اصلاح باگ در فرم ورود").
- **به‌روزرسانی شاخه‌ها**: به طور منظم از `develop` pull/rebase کنید تا از تعارض‌ها جلوگیری شود.
- **Rebase در مقابل Merge**: شاخه‌های خصوصی را rebase کنید برای تاریخچه تمیز؛ هرگز شاخه‌های اشتراکی را rebase نکنید. از merge برای ادغام استفاده کنید.
- **پیاده‌سازی CI/CD**: از trunk شاخه بگیرید، از PRها استفاده کنید و تست‌ها/بیلدها را روی تمام شاخه‌ها خودکار کنید.
- **بدون ویژگی جدید در Release/Hotfix**: فقط اصلاح باگ و متادیتا.
- **استفاده از --no-ff برای Mergeها**: تاریخچه را حفظ می‌کند و بازگشت آسان را اجازه می‌دهد.
- **تگ انتشارها**: همیشه mergeهای `master` را برای ردیابی تگ کنید.
- **خودکارسازی استقرارها**: از هوک‌های Git روی `master` برای بیلد/استقرار استفاده کنید.
- **تطبیق با اندازه تیم**: برای تیم‌های کوچک، نسخه‌های سبک‌تر را در نظر بگیرید؛ برای بزرگ، بررسی PR را اجباری کنید.
- **شاخه‌های Feature در ریپوهای محلی**: فقط اگر همکاری می‌کنید پوش کنید.
- **مدیریت تعارض‌های ادغام زود**: به ویژه افزایش نسخه در release/hotfixها.

Git Flow در کنار دستورات Git ساده کار می‌کند. از شاخه‌های 'support' بتا اجتناب کنید.

## منابع
- [Git Flow Cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/index.fa_FA.html)
- [مدل شاخه‌بندی Git اصلی](https://nvie.com/posts/a-successful-git-branching-model/)
- [آموزش Git Flow Atlassian](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [راهنمایی Git Flow AWS](https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-git-branch-approach/gitflow-branching-strategy.html)

برای تمرین، این ریپو را کلون کنید و با دستورات آزمایش کنید!

## دیاگرام مدل شاخه‌بندی Git

![مدل شاخه‌بندی Git](https://nvie.com/img/git-model@2x.png)
```

با این تنظیمات، ریپازیتوری شما دو زبانه خواهد بود و کاربران می‌توانند به راحتی بین نسخه‌ها جابجا شوند. اگر نیاز به تغییرات بیشتری دارید، بگید!
