# 🏃 گزارش ورزش، خواب و انرژی

> اپلیکیشن شخصی تک‌فایل HTML برای ثبت و تحلیل روزانه ورزش، خواب، انرژی و فوتسال با تقویم شمسی، تحلیل هوشمند، sync ابری و ۸ نمودار پیشرفته

**نسخه:** v4.3  
**Build:** 1405-04-25

---

## 📋 فهرست

- [هدف برنامه](#-هدف-برنامه)
- [ویژگی‌های اصلی](#-ویژگی‌های-اصلی)
- [معماری فنی](#️-معماری-فنی)
- [مدل داده](#-مدل-داده-schema)
- [ساختار UI](#-ساختار-ui)
- [منطق تحلیل هوشمند](#-منطق-تحلیل-هوشمند)
- [سیستم احراز هویت](#-سیستم-احراز-هویت)
- [Cloud Sync](#️-cloud-sync-jsonbinio)
- [سیستم Backup چند لایه](#-سیستم-backup-چند-لایه)
- [Export System](#-export-system)
- [نمودارها](#-نمودارها-8-تا)
- [تم و ظاهر](#-تم-و-ظاهر)
- [Health Check](#-health-check)
- [مسائل شناخته شده](#-مسائل-شناخته-شده-و-راه‌حل‌ها)
- [دستورات Debug](#-دستورات-مفید-debug)
- [راهنمای Deploy](#-راهنمای-deploy)
- [راهنما برای AI](#-راهنما-برای-ai-دستیار)

---

## 🎯 هدف برنامه

اپلیکیشن شخصی برای:

- ثبت روزانه ورزش (دویدن، پیاده‌روی)، خواب چندبازه‌ای، انرژی، حال روحی
- تحلیل هوشمند الگوها و پیشنهاد بر اساس ساعت روز (۸ سناریو زمانی)
- پیگیری جلسات فوتسال به صورت جامع
- محاسبه‌گرهای علمی (خواب، ریکاوری، برنامه دویدن)
- Sync ابری بین گوشی و لپ‌تاپ
- تولید خروجی مناسب برای AI برای تحلیل عمیق‌تر
- سیستم احراز هویت برای امنیت شخصی
- ۸ نمودار مختلف برای بررسی روند

---

## ⭐ ویژگی‌های اصلی

```
✅ Login system با SHA-256 hash + salt
✅ ذخیره ابری با JSONBin (بدون فیلتر از ایران)
✅ Auto-sync هوشمند (15 دقیقه، فقط اگه تغییر باشه)
✅ یادآور backup هفتگی
✅ ۸ نمودار مختلف (شامل Sleep Heatmap)
✅ Health Check دکمه‌ای برای عیب‌یابی
✅ تحلیل هوشمند با ۸ سناریو زمانی
✅ ۵ تم رنگی + ۴ فونت فارسی + اندازه قابل تنظیم
✅ Responsive کامل موبایل + دسکتاپ
✅ RTL + شمسی
```

---

## 🏗️ معماری فنی

### تک‌فایل HTML

- **همه چیز** تو یه فایل `index.html` — CSS + JS + HTML inline
- **کتابخانه‌ها از CDN**:
  - Chart.js 4.4.7
  - jalaali-js 1.2.6 (تقویم جلالی)
- **فونت‌ها**:
  - Vazirmatn (Google Fonts)
  - Sahel + Samim (jsdelivr CDN)
- **بدون build step** — مستقیم روی GitHub Pages deploy میشه
- **حجم**: ~180KB

### ذخیره‌سازی

```
localStorage keys:
├── sport_report_v2        → دیتای اصلی (rows, futsal, goals, programGoal)
├── sport_accent_v3        → تم رنگی
├── sport_font_v1          → فونت انتخابی
├── sport_font_scale_v1    → اندازه فونت (0.8x - 1.5x)
├── sport_cloud_v2         → {apiKey, binId, autoSync, lastSync}
├── sport_auth_v1          → {username, hash (SHA-256), salt}
├── sport_session_v1       → {t: timestamp}
└── sport_last_backup_v1   → timestamp آخرین بکاپ محلی
```

### Sync ابری (بهینه‌شده)

- **سرویس**: JSONBin.io (رایگان، 10K request/ماه)
- **متد**: PUT برای upload، GET برای download
- **Auto-sync**: هر ۱۵ دقیقه چک، **فقط اگه تغییری باشه** upload میشه
- **Debounce**: ۱۰ ثانیه تأخیر بعد از تغییرات
- **Hash check**: مقایسه با آخرین وضعیت sync شده
- **مصرف واقعی**: ~۳۰۰-۵۰۰ request/ماه (از ۱۰,۰۰۰ سقف = ۵٪ فقط)
- **Privacy**: Bin با تیک Private ساخته میشه

### امنیت

- **رمز عبور**: SHA-256 با salt تصادفی، فقط تو localStorage
- **Session**: 7 روز اعتبار
- **Bin Private**: فقط با API key دسترسی
- **کد Public**: کد GitHub public هست ولی دیتای شخصی توش نیست

---

## 📊 مدل داده (schema)

```typescript
{
  rows: [
    {
      id: number,           // Date.now()
      jdate: string,        // "1405-04-21" (شمسی)
      completed: boolean,
      energy: number,       // 0-10 (با گام 0.5)
      mood: number,         // 0-10
      runMin: number,       // دقیقه
      walkMin: number,      // دقیقه
      longestRun: number,   // بیشترین دویدن پیوسته (دقیقه)
      activity: string,     // شرح فعالیت
      note: string,         // یادداشت
      futsalRef: boolean,   // مرتبط با فوتسال؟
      sleepBlocks: [
        {
          label: string,    // "خواب شب" | "چرت" | "چرت صبحگاهی" | ...
          start: string,    // "HH:MM"
          end: string       // "HH:MM"
        }
      ],
      officialWake: string | null,  // "HH:MM"
      events: []            // reserved for future
    }
  ],
  futsal: [
    {
      id: number,
      jdate: string,
      label: string,               // "جمعه ۲۲ خرداد"
      bedtimeBefore: string,       // "HH:MM"
      energyBefore: number,        // 0-10
      fatigueBefore: number,       // 0-10
      durationActual: number,      // دقیقه
      intensity: number,           // 1-10
      restCount: number,
      breathless: string,
      hadToStop: boolean,
      dizziness: string,
      calfPain: string,
      thighPain: string,
      kneePain: string,
      chestPain: string,
      energyAfter: number,
      fatigueAfter: number,
      moodAfter: number,
      comparedToExpected: string,
      continuousRun: string,
      hardestPart: string,
      bestPart: string,
      extra: string
    }
  ],
  notes: string[],
  goals: {
    sleepGoalHours: number,        // پیش‌فرض 7
    activityGoalMin: number        // پیش‌فرض 20
  },
  programGoal: string              // متن هدف کلی برنامه
}
```

---

## 🎨 ساختار UI

### تب‌های اصلی (۷ تا):

| # | تب | آیکون | توضیح |
|---|-----|-------|-------|
| 1 | داشبورد | 🏠 | KPIs، Streak map، Insights، ۷ روز اخیر، تایم‌لاین خواب، مقایسه هفتگی، اهداف، یادآور backup |
| 2 | ثبت روزانه | 📋 | لیست + جستجو + ویرایش/حذف |
| 3 | فوتسال | ⚽ | لیست جلسات + ثبت جامع |
| 4 | نمودارها | 📊 | **۸ نمودار** — جزئیات پایین |
| 5 | محاسبه‌گر | 🧮 | ۴ ابزار: زمان خواب، شدت فعالیت، برنامه دویدن، ریکاوری |
| 6 | تحلیل | 🧠 | پیشنهاد بر اساس ساعت (۸ سناریو)، روند، الگوها، نکات علمی |
| 7 | خروجی | 📤 | Export منعطف با ۴ فرمت + Cloud + Import |

### مودال ثبت روز (5 مرحله stepper):

1. **مرحله ۰**: تاریخ + وضعیت + مرتبط با فوتسال
2. **مرحله ۱**: انرژی + حال (rating slider با دکمه‌های 0-10 و half-toggle)
3. **مرحله ۲**: دویدن + پیاده + بیشترین دویدن + شرح
4. **مرحله ۳**: بلاک‌های خواب (multi-block) + بیداری رسمی
5. **مرحله ۴**: یادداشت

### موبایل:

- Bottom nav با ۵ تب اصلی + منوی **⋯ بیشتر** برای بقیه
- FAB (Floating Action Button) برای ثبت سریع
- طراحی responsive از 320px تا 4K
- Touch-friendly (دکمه‌ها حداقل 44x44px)

---

## 🧠 منطق تحلیل هوشمند

### فرمول Readiness Score (WHOOP-inspired)

```javascript
sleepScore = (yesterdaySleepHours / 8) * 100
energyScore = (yesterdayEnergy / 10) * 100
consistencyScore = (last7DaysActive / 7) * 120

readiness = sleepScore*0.4 + energyScore*0.35 + consistencyScore*0.25

// طبقه‌بندی:
// 75+  → فعالیت شدید مجاز 💪
// 55-75 → فعالیت متوسط ⚡
// 35-55 → فعالیت سبک 🚶
// <35  → استراحت 🧘
```

### پیشنهاد بر اساس ساعت (۸ سناریو):

| ساعت | وضعیت | پیشنهاد |
|------|-------|---------|
| 05-09 | 🌅 صبح زود | بهترین زمان — کورتیزول طبیعی بالاست |
| 09-12 | ☀️ صبح | فعالیت با شدت متوسط تا زیاد |
| 12-15 | 🌞 ظهر | احتیاط — گرما + خستگی طبیعی |
| 15-18 | 🌤️ عصر | **بهترین زمان علمی** — قدرت اوج |
| 18-21 | 🌆 غروب | فعالیت سبک تا متوسط |
| 21-24 | 🌙 شب | آماده خواب — استرچ OK |
| 00-03 | 🌃 نصف شب | خوابت نبرده؟ تنفس ۴-۷-۸، بابونه |
| 03-05 | 🌌 نصفه‌های شب | تلاش برای خواب |

### الگوها (Pattern Analysis):

- **Pearson correlation** بین خواب دیشب و انرژی فردا
- **Bedtime analysis**: مقایسه انرژی وقتی زود می‌خوابی vs دیر
- **Weekday patterns**: کدوم روز هفته بیشترین فعالیت
- **Post-futsal recovery**: میانگین انرژی فردای فوتسال
- **Nap analysis**: تعداد چرت‌ها = نشانه خواب شب ناکافی

### نکات علمی (Tips):

- کم‌خوابی <6.5h = افت 20-30% عملکرد ورزشی
- Progressive Overload: هدف بعدی = رکورد فعلی × 1.2
- Consistency > Intensity: 10 دقیقه ثابت > 1 ساعت هر 2 هفته
- Melatonin بین 21-23 ترشح میشه
- Recovery بعد فوتسال: 8h خواب + 2.5L آب + 20-30g پروتئین

---

## 🔐 سیستم احراز هویت

### First-time Setup:
- صفحه ورود قشنگ با گرادیانت
- ثبت‌نام: username + password + confirm
- SHA-256 با salt تصادفی
- ذخیره فقط تو localStorage همون دستگاه

### Login:
- username + password
- بررسی hash مقایسه با ذخیره شده
- ایجاد session (7 روز)

### Session:
- اعتبار 7 روز
- بعد از expire، دوباره لاگین لازم

### Reset:
- تایپ کلمه `RESET` → پاک شدن رمز
- **دیتا حفظ میشه**
- امکان ست کردن رمز جدید

### Multi-device:
- هر دستگاه رمز مستقل داره
- می‌تونی رمز‌های متفاوت رو دستگاه‌های مختلف بذاری
- دیتا با ابر sync میشه

---

## ☁️ Cloud Sync (JSONBin.io)

### Setup اولیه (فقط یه بار):

1. کاربر تو [jsonbin.io](https://jsonbin.io) ثبت‌نام
2. API Key رو کپی می‌کنه (Master Key که با `$2a$10$` شروع میشه)
3. یه Private Bin می‌سازه با محتوای اولیه:
   ```json
   {
     "rows": [],
     "futsal": [],
     "notes": [],
     "goals": {"sleepGoalHours": 7, "activityGoalMin": 20},
     "programGoal": "شروع"
   }
   ```
4. Bin ID رو از URL کپی می‌کنه (24 کاراکتری)
5. تو اپ paste می‌کنه

### API Endpoints:

```javascript
// Upload (Update)
PUT https://api.jsonbin.io/v3/b/{binId}
Headers: 
  Content-Type: application/json
  X-Master-Key: {apiKey}
Body: JSON.stringify(state)

// Download (Read)
GET https://api.jsonbin.io/v3/b/{binId}/latest
Headers: 
  X-Master-Key: {apiKey}
Response: { record: {...state}, metadata: {...} }
```

### Auto-sync (بهینه‌شده در v4.2):

- **هر ۱۵ دقیقه چک میشه** (نه upload کور)
- **فقط اگه تغییری باشه** upload میشه (با مقایسه hash)
- بعد از هر ثبت/تغییر با ۱۰ ثانیه تأخیر (که تغییرات متعدد یکجا sync شن)
- Badge وضعیت بالای صفحه: ☁️ ✓ (سبز) / ⏳ (زرد) / ✗ (قرمز)

### مصرف واقعی:

با بهینه‌سازی جدید:
- ✅ ۳۰۰-۵۰۰ request/ماه (از ۱۰,۰۰۰ سقف)
- ✅ ۹۵٪+ باقی می‌مونه
- ✅ بدون قطع سرویس

---

## 💾 سیستم Backup چند لایه

### لایه ۱: localStorage (خودکار)
- بعد از هر تغییر ذخیره میشه
- روی هر دستگاه مستقل
- سریع‌ترین، ولی محدود به همون مرورگر

### لایه ۲: JSONBin (خودکار)
- Auto-sync هر ۱۵ دقیقه (فقط اگه تغییری باشه)
- 10 ثانیه بعد از هر ثبت
- بین دستگاه‌ها sync میشه
- **مصرف بهینه**: ~300-500 request/ماه از ۱۰,۰۰۰

### لایه ۳: Backup محلی (نیمه‌خودکار)
- **یادآور هفتگی**: بنر نارنجی بالای داشبورد
- بعد از ۷ روز از آخرین backup نمایش داده میشه
- ۳ گزینه:
  - 📤 **بگیر**: دانلود JSON + reset timer
  - **۲ روز بعد**: تعویق کوتاه
  - **✕**: dismiss این هفته

### چرا تلگرام نه؟
- ❌ api.telegram.org از ایران فیلتره
- ❌ نیاز به VPN دائم = ناپایدار
- ✅ راه‌حل: بجاش یادآور دانلود محلی

### توصیه برای کاربر:

```
هفته‌ای یه بار (وقتی بنر میاد):
1. کلیک 📤 بگیر
2. فایل JSON دانلود میشه
3. یه جای امن نگه دار:
   - Google Drive (توصیه)
   - USB
   - Email به خودت
   - فولدر خاص روی لپ‌تاپ
```

---

## 📤 Export System

### فرمت‌ها:

1. **AI Compact** (پیش‌فرض)
   - متن ساختاریافته
   - شامل هدف، آمار، روزها، فوتسال، الگوها
   - Prompt نهایی برای AI

2. **Markdown**
   - گزارش قابل خوندن
   - جدول آمار
   - H3 برای هر روز

3. **JSON**
   - minified برای backup
   - قابل Import مجدد

4. **CSV**
   - UTF-8 BOM
   - escape درست
   - قابل باز شدن در Excel

### Checkboxes قابل انتخاب:

- 🎯 هدف برنامه
- 📊 آمار کلی
- 📅 روزها (با یادداشت کوتاه/کامل/بدون)
- ⚽ فوتسال
- 🔬 الگوها
- 💡 پیشنهادات

### بازه:

- همه روزها
- ۷ روز اخیر
- ۱۴ روز اخیر
- ۳۰ روز اخیر
- سفارشی (با تقویم شمسی)

### روش‌های انتقال:

- 📋 کپی به clipboard
- 💾 دانلود فایل
- 📤 Share (Web Share API)

---

## 📊 نمودارها (8 تا)

همه نمودارها **هر بار که تب رو باز کنی**، از دیتای جدید ساخته میشن. کاملاً responsive برای موبایل.

### 1. 😴 خواب (Line)
- خط اصلی: خواب روزانه
- خط دوم: میانگین ۷ روزه (Moving Average)
- خط سوم: هدف (dashed قرمز)

### 2. ⚡ انرژی و حال (Line)
- ۲ خط اصلی: انرژی + حال
- ۲ خط MA7: میانگین ۷ روزه هرکدوم

### 3. 🏃 فعالیت (Stacked Bar)
- دویدن + پیاده‌روی
- Stack به صورت رنگی

### 4. 🔥 هیت‌مپ خواب (Heatmap) ⭐ جدید
- گرید مثل GitHub contributions
- هر روز یه خونه
- ۶ سطح رنگی برای مقدار خواب:
  - 🔴 قرمز: <4h
  - 🟡 زرد: 4-5.5h
  - 🟣 بنفش کم: 5.5-6.5h
  - 🟣 بنفش پر: 6.5-7.5h
  - 🟢 سبز روشن: 7.5-9h
  - 🟢 سبز پر: 9+h
- Tooltip روی hover/tap
- Legend راهنما

### 5. 🌙 ساعت خواب (Line) ⭐ جدید
- ۲ خط: ساعت خواب + ساعت بیداری
- محور Y با فرمت ساعت (18:00, 20:00, ...)
- بعد نصف شب (00-06) درست نمایش داده میشه
- Tooltip با فرمت HH:MM

### 6. 📅 مقایسه هفتگی (Bar) ⭐ جدید
- ۵ dataset گروهی:
  - 😴 خواب (h)
  - ⚡ انرژی
  - 🙂 حال
  - 🏃 دویدن (÷۵)
  - ✅ روزهای فعال
- می‌تونی dataset‌ها رو با کلیک روی legend خاموش/روشن کنی

### 7. 🕸️ رادار (Radar)
- مقایسه ۳ هفته آخر
- ۵ محور: خواب، انرژی، حال، دویدن، پیاده

### 8. 🔬 همبستگی (Scatter)
- خواب امشب vs انرژی فردا
- تشخیص pattern
- Pearson correlation

---

## 🎨 تم و ظاهر

- **دارک بنفش** (پیش‌فرض)
- **۵ تم رنگی**:
  - 🟣 بنفش (`#8b5cf6`)
  - 🔵 آبی (`#0ea5e9`)
  - 🟢 سبز (`#10b981`)
  - 🟡 طلایی (`#f59e0b`)
  - 🌸 صورتی (`#ec4899`)
- **۴ فونت فارسی**:
  - Vazirmatn (پیش‌فرض ⭐)
  - Sahel
  - Samim
  - Tahoma (سیستم)
- **اندازه فونت**: A- / پیش‌فرض / A+ (0.8x تا 1.5x)
- **RTL کامل**
- **Backdrop blur** + گرادیانت‌های بنفش
- **Anime**های ظریف برای:
  - Fade in views
  - Slide up modals
  - Count-up KPIs
  - Ripple button clicks

---

## 🩺 Health Check

سیستم عیب‌یابی داخلی برای بررسی سلامت اپ.

### دسترسی:
**⚙️ → 🩺 تست سلامت**

### چیزهایی که چک می‌کنه:

```
📦 نسخه
├── Version + Build

🔐 لاگین
├── Auth setup
└── Session active

📊 دیتا
├── تعداد rows
├── تعداد فوتسال
└── Goals

☁️ ابری
├── API Key set
├── Bin ID set
├── Auto Sync
└── Last sync time

⚡ بهینه‌سازی Sync
├── Auto interval 15min
├── Hash check
└── Debounce 10sec

💾 یادآور Backup
├── تابع Backup موجود
├── آخرین backup
└── وضعیت بنر

💽 حافظه
├── Total size
└── Rows size

🎨 ظاهر
├── Theme
├── Font
└── Font Scale

🌐 مرورگر
├── Screen size
└── Mobile detection

📋 خلاصه (نتیجه کلی)
```

### دکمه‌ها:
- 🔄 **مجدد**: آپدیت نتایج
- 📋 **کپی**: کپی به clipboard برای ارسال به AI

---

## 🐛 مسائل شناخته شده و راه‌حل‌ها

### 0. مشکلات از ایران

- ❌ **تلگرام API**: فیلتره → استفاده نشد
- ✅ **JSONBin**: کار می‌کنه
- ✅ **GitHub**: کار می‌کنه (کد + Pages)
- ✅ **Google Fonts + jsdelivr CDN**: کار می‌کنه
- ✅ **api.telegram.org**: فیلتره → از یادآور محلی بجاش استفاده شد

**این پروژه ۱۰۰٪ از ایران بدون VPN کار می‌کنه.**

### 1. Font در بار اول کند لود میشه
**راه‌حل**: `<link preconnect>` + `font-display: swap`

### 2. Time input تو موبایل کوچیک
**راه‌حل**: `min-height: 46px` + `font-size: 1rem`

### 3. Grid 2-column خیلی فشرده تو موبایل کوچیک
**راه‌حل**:
```css
@media(max-width:759px){ 
  .grid-2{grid-template-columns:1fr} 
}
```

### 4. Bin ID خودکار ساخته نمیشه
**راه‌حل**: کاربر باید دستی تو JSONBin بسازه — API عمومی برای Create Bin نداره

### 5. تقویم بازتر از صفحه
**راه‌حل**: موقعیت‌دهی هوشمند با `window.innerHeight/Width`

### 6. Session بعد ۷ روز expire میشه
**راه‌حل**: طراحی شده، امنیتیه — با logout دستی هم قابل انجامه

### 7. Rating slider default یا 5 یا 5.5؟
**راه‌حل**: default = 5.0 (half toggle به‌طور پیش‌فرض روی "صفر")

### 8. Heatmap روی موبایل با دیتای زیاد اسکرول عمودی داره
**راه‌حل**: طبیعیه — هر هفته یه ردیف

---

## 🔧 دستورات مفید Debug

### تو Console مرورگر (F12):

```javascript
// دیدن کل state
console.log(JSON.parse(localStorage.getItem('sport_report_v2')));

// backup سریع به clipboard
copy(localStorage.getItem('sport_report_v2'));

// پاک کردن فقط رمز (بدون از دست دادن دیتا)
localStorage.removeItem('sport_auth_v1');
localStorage.removeItem('sport_session_v1');
location.reload();

// reset کامل (همه چیز)
Object.keys(localStorage)
  .filter(k => k.startsWith('sport_'))
  .forEach(k => localStorage.removeItem(k));
location.reload();

// دسترسی به app object
window._app.state();          // دیتای فعلی
window._app.Jalali.today();   // تاریخ شمسی امروز
window._app.computeStats(window._app.state().rows);  // آمار

// force render
window._app.renderDashboard();
window._app.renderAIView();

// ری‌ست یادآور backup (که همین الان بیاد)
localStorage.removeItem('sport_last_backup_v1');
window._app.renderDashboard();

// force backup دانلود
window._app.doBackupNow();

// چک آخرین بکاپ
const last = localStorage.getItem('sport_last_backup_v1');
console.log('Last backup:', last ? new Date(parseInt(last)).toLocaleString('fa-IR') : 'هرگز');

// چک وضعیت sync
const cc = JSON.parse(localStorage.getItem('sport_cloud_v2') || '{}');
console.log('Cloud config:', cc);
console.log('Last sync:', cc.lastSync ? new Date(cc.lastSync).toLocaleString('fa-IR') : 'هرگز');
```

### مشکلات رایج:

**مشکل**: صفحه سفید بعد از آپدیت  
**حل**: F12 → Console → ببین چه خطایی نشون میده. معمولاً یه پرانتز/کاما اضافه‌ست.

**مشکل**: دیتا از دست رفت  
**حل**: 
1. اول `📤 خروجی → 💾 دانلود JSON` بگیر  
2. `Import JSON` کن  
3. یا از JSONBin `⬇️ بارگذاری`

**مشکل**: sync کار نمی‌کنه  
**حل**:
1. **⚙️ → 🩺 تست سلامت** بزن
2. ببین Cloud بخش چی میگه
3. Console خطا نشون میده؟
4. Bin ID و API Key درسته؟
5. اینترنت داری؟

---

## 🚀 راهنمای Deploy

### روی GitHub Pages:

1. Fork/Clone این repo
2. تغییرات لازم رو تو `index.html` انجام بده
3. Settings → Pages → Deploy from branch `main`
4. لینک: `https://{username}.github.io/{repo}/`

### روی Netlify:

1. app.netlify.com → Add new site → Deploy manually
2. Drag `index.html`
3. لینک تصادفی دریافت کن

### محلی (offline):

1. فایل `index.html` رو دانلود کن
2. دبل‌کلیک — تو مرورگر باز میشه
3. (Sync آنلاین نیاز به اینترنت داره)

---

## 📱 استفاده به عنوان PWA

**Chrome/Brave (Android):**
1. سایت رو باز کن
2. منوی ⋮
3. Add to Home screen
4. آیکون تو صفحه اصلی

**Safari (iOS):**
1. سایت رو باز کن
2. دکمه Share
3. Add to Home Screen

**نکات:**
- کار می‌کنه حتی آفلاین (بعد از first load)
- فقط sync ابری نیاز به اینترنت داره
- دیتای localStorage محلی رو دستگاه هست

---

## 🤖 راهنما برای AI دستیار

اگه AI هستی و می‌خوای تو این پروژه کمک کنی، این بخش برای توئه.

### چیزهایی که باید بدونی:

1. **زبان اصلی UI**: فارسی، RTL
2. **تقویم**: جلالی (شمسی) — استفاده از `jalaali-js`
3. **کد ساختار**: تک فایل HTML، IIFE pattern، `window._app` namespace
4. **State**: singleton pattern، از `localStorage.sport_report_v2`
5. **کاربر**: فارسی‌زبان، اولویت گوشی، دقیق و حساس به جزئیات UI

### الگوی درخواست کاربر:

> "این باگ رو داره فیکس کن ولی دیتام دست نخوره"

پس همیشه:

- ✅ schema data رو حفظ کن
- ✅ localStorage keys رو دست نزن
- ✅ backward compatibility رعایت کن
- ✅ پچ کوچک بده اگه امکانش هست
- ✅ فایل کامل بده فقط اگه لازمه

### برای اعمال پچ:

1. **دقیق مشخص کن کجای کد باید تغییر کنه** (با `Ctrl+F` قابل پیدا کردن)
2. **کل بلوک رو بگو** نه فقط چند خط
3. **حتماً از localStorage compatibility** مطمئن شو (schema رو تغییر نده)
4. **بعد پچ، از کاربر بخواه Commit → Refresh → Test**
5. **از Health Check برای verify کردن پچ استفاده کن**

### نقاط قوت که نباید دست بخوره:

- ✅ سیستم لاگین با SHA-256
- ✅ Rating slider (0-10 با گام 0.5)
- ✅ تحلیل هوشمند بر اساس ساعت (۸ سناریو)
- ✅ Sync ابری با JSONBin (auto-sync 15 دقیقه، فقط اگه تغییر باشه)
- ✅ تقویم جلالی (jalaali-js)
- ✅ Stepper 5-مرحله‌ای ثبت روز
- ✅ Bottom nav با منوی "بیشتر" برای موبایل
- ✅ یادآور backup هفتگی (banner بالای داشبورد)
- ✅ ۳ لایه backup: localStorage + JSONBin + دستی
- ✅ Health Check دکمه‌ای برای عیب‌یابی
- ✅ ۸ نمودار مختلف (شامل Sleep Heatmap)

### چیزهایی که هنوز جا برای بهبود دارن:

- 📊 نمودارهای بیشتر (comparison, quality score)
- 🔔 Push Notification یادآور
- 📸 عکس گرفتن از غذا/ورزش
- 💧 ردیابی مصرف آب
- ⚖️ ثبت وزن روزانه
- 📅 export PDF قشنگ‌تر
- 🌐 چند زبانه (فعلاً فقط فارسی)
- ☁️ Backup به Google Drive (چون تلگرام از ایران فیلتره)
- 🔄 Sync دو طرفه هوشمند (تشخیص conflict)

### فایل‌های پروژه:

```
my-sport-reports-only/
├── index.html      ← کل برنامه
└── README.md       ← این فایل
```

### سبک کد:

- **CSS**: BEM-ish + Utility classes
- **JS**: Vanilla ES6+، IIFE، بدون framework
- **HTML**: semantic + accessible
- **Comments**: فارسی + انگلیسی (mix)

### توابع مهم که ممکنه تغییر بدی:

| تابع | مسئولیت |
|------|---------|
| `Jalali.*` | تقویم جلالی |
| `sleepInfo(blocks)` | محاسبه خواب چندبازه‌ای |
| `computeStats(rows)` | آمار |
| `generateRecommendation()` | پیشنهاد ساعت (۸ سناریو) |
| `analyzePatterns()` | الگوها (Pearson correlation) |
| `renderDashboard()` | داشبورد |
| `openDayModal(id)` | مودال ثبت (stepper 5 مرحله‌ای) |
| `cloudUpload()` / `cloudDownload()` | Sync JSONBin |
| `exportAI()` | خروجی AI |
| `checkBackupReminder()` | یادآور backup هفتگی |
| `renderSleepHeatmap()` | نمودار Heatmap |
| `renderAllCharts()` | همه ۸ نمودار |
| `A.doBackupNow()` | دانلود سریع JSON |
| `A.runHealthCheck()` | تست سلامت سیستم |
| `openMoreSheet()` | منوی "بیشتر" موبایل |

### تست پس از هر تغییر:

- [ ] لاگین کار می‌کنه؟
- [ ] ثبت روز جدید ذخیره میشه؟
- [ ] ویرایش کار می‌کنه؟
- [ ] نمودارها رندر میشن؟ (همه ۸ تا)
- [ ] Sync ابری OK؟ (Health Check چک کن)
- [ ] تم و فونت حفظ شد؟
- [ ] موبایل responsive؟
- [ ] Backup reminder کار می‌کنه؟

### چطور یک AI جدید رو با پروژه آشنا کنی:

**قالب پیام:**

```
سلام! من یه اپ شخصی دارم. لطفاً اول این README رو کامل بخون:

🔗 راهنما: 
https://raw.githubusercontent.com/{USERNAME}/{REPO}/main/README.md

🔗 کد کامل:
https://raw.githubusercontent.com/{USERNAME}/{REPO}/main/index.html

🌐 سایت لایو:
https://{USERNAME}.github.io/{REPO}/

بعد از خواندن، بگو "آماده کمکم" تا سوالم رو بپرسم.
```

---

## 🔔 سیستم یادآور Backup

### هدف:
یادآوری هفتگی به کاربر که backup محلی بگیره (مکمل sync ابری).

### مکانیزم:
```javascript
LS_KEY: 'sport_last_backup_v1'
INTERVAL: 7 روز
```

### زمان نمایش بنر:
- در `renderDashboard()` تابع `checkBackupReminder()` صدا زده میشه
- اگه `Date.now() - lastBackup >= 7*24*60*60*1000` → بنر نمایش داده میشه
- اگه اصلاً backup محلی نگرفته: `daysSince = 999` (فوراً نمایش)

### گزینه‌های کاربر:
| دکمه | عملکرد | تاثیر روی timer |
|------|--------|-----------------|
| 📤 بگیر | دانلود JSON فایل | reset به 7 روز دیگه |
| ۲ روز بعد | فقط بستن بنر | 2 روز دیگه نمایش |
| ✕ | dismiss این هفته | 7 روز دیگه نمایش |

### فرمت نام فایل:
```
sport-backup-1405-04-25.json
```

---

## 📖 مطالعات علمی مرتبط

منابع استفاده شده برای فرمول‌ها:

- **WHOOP Recovery Score**: readiness metric
- **Chronobiology & Athletic Performance**: بهترین ساعت ورزش
- **Sleep Foundation**: نیازهای خواب بزرگسال
- **Progressive Overload Principle**: افزایش تدریجی 10-20%
- **Melatonin Studies**: زمان‌بندی خواب

---

## 📞 تماس و مشارکت

- **GitHub Issues**: برای گزارش باگ
- **Fork**: برای شخصی‌سازی
- **Pull Request**: برای بهبود

---

## 📄 لایسنس

**استفاده شخصی رایگان**

هر کس می‌تونه:
- ✅ Fork کنه
- ✅ برای خودش استفاده کنه
- ✅ تغییر بده
- ✅ به دوستان بده

---

## 📅 Changelog

### v4.3 (1405-04-25)
- ✨ **جدید**: ۳ نمودار اضافه شد (Heatmap خواب، ساعت خواب، مقایسه هفتگی)
- ✨ **جدید**: Health Check دکمه‌ای در تنظیمات
- ✨ **جدید**: یادآور backup هفتگی با بنر نارنجی
- ⚡ **بهبود**: Auto-sync از ۵ به ۱۵ دقیقه (با hash check)
- ⚡ **بهبود**: Debounce از ۳ به ۱۰ ثانیه
- 📉 **کاهش**: مصرف JSONBin ۹۵٪ کمتر شد

### v4.2 (1405-04-24)
- ✅ سیستم لاگین با SHA-256
- ✅ منوی "بیشتر" برای موبایل
- ✅ فیکس‌های موبایل

### v4.0 (1405-04-20)
- ✅ Cloud sync با JSONBin
- ✅ تحلیل هوشمند ۸ سناریو زمانی
- ✅ بازطراحی UI

---

## 🎉 تشکر

- [jalaali-js](https://github.com/jalaali/jalaali-js) — تقویم جلالی
- [Chart.js](https://www.chartjs.org/) — نمودارها
- [Vazirmatn](https://github.com/rastikerdar/vazirmatn-font) — فونت فارسی
- [JSONBin.io](https://jsonbin.io) — Cloud storage رایگان

---

**Made with ❤️ in Iran**
