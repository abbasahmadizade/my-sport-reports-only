<div dir="rtl" align="center">

# 🏃 Sport Report

**اپلیکیشن شخصی ثبت و تحلیل ورزش، خواب و انرژی — با تقویم شمسی**

[![GitHub Pages](https://img.shields.io/badge/GitHub_Pages-deployed-2ea44f?style=for-the-badge&logo=github)](https://abbasahmadizade.github.io/my-sport-reports-only/)
[![Vanilla JS](https://img.shields.io/badge/Vanilla_JS-No_Framework-f7df1e?style=for-the-badge&logo=javascript)](#)
[![RTL](https://img.shields.io/badge/UI-Persian_RTL-blue?style=for-the-badge)](#)
[![v5.2](https://img.shields.io/badge/نسخه-5.2-8b5cf6?style=for-the-badge)](#)

**۱۰۰٪ از ایران بدون VPN کار می‌کنه** ✅ &nbsp;|&nbsp; [دمو زنده 🚀](https://abbasahmadizade.github.io/my-sport-reports-only/)

_ساخته‌شده با ❤️ توسط [Arena.ai](https://arena.ai)_

</div>

---

<div dir="rtl" align="right">

## ✨ امکانات

</div>

<div dir="rtl" align="right">

| | ویژگی | توضیح |
|---|---|---|
| 🔐 | **Login با SHA-256** | رمز + salt تصادفی، session ۷ روزه |
| ☁️ | **Cloud Sync** | همگام‌سازی با GitHub Gist — بدون فیلتر |
| 🗺️ | **نقشه فعالیت** | GitHub-style، گرادیان ۵ سطح، tooltip فارسی |
| ⏱️ | **ورود mm:ss** | ثبت دقیقه:ثانیه برای دویدن و پیاده‌روی |
| 📊 | **۸ نمودار** | Line / Bar / Radar / Scatter / Heatmap |
| 🧠 | **تحلیل هوشمند** | Readiness Score + ۸ سناریو زمانی |
| 📤 | **Export** | AI / Markdown / JSON / CSV |
| 🎨 | **۵ تم + ۴ فونت** | دارک RTL، کاملاً فارسی |
| 📱 | **موبایل‌فرست** | Responsive از 320px |

</div>

---

<div dir="rtl" align="right">

## 🏗️ معماری

</div>

<div dir="ltr" align="center">

```
┌──────────────────────────────────────────────────────────────┐
│                       index.html (~11,000 lines)             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   ┌──────────┐     ┌──────────────┐     ┌──────────────┐    │
│   │   Core   │────▶│    Render    │────▶│    Events    │    │
│   │  Jalali  │     │  Dashboard   │     │    Cloud     │    │
│   │  State   │     │   Charts     │     │    Export    │    │
│   │  Stats   │     │   Map        │     │              │    │
│   └────┬─────┘     └──────────────┘     └──────┬───────┘    │
│        │                                        │            │
│        ▼                                        ▼            │
│   ┌───────────┐                         ┌──────────────┐    │
│   │ 💾 local  │                         │ ☁️ GitHub    │    │
│   │  Storage  │                         │    Gist      │    │
│   └───────────┘                         └──────────────┘    │
│                                                              │
│   📦 CDN: Chart.js 4.4.7 · jalaali-js 1.2.6 · Vazirmatn   │
└──────────────────────────────────────────────────────────────┘
```

</div>

<div dir="ltr" align="left">

```
index.html
├── <style>         ── Themes, RTL, Components
├── <script> 1-3   ── Core (Jalali · State · Stats · Helpers)
├── <script> 4     ── Render (Dashboard · Log · Charts · Map)
└── <script> 5     ── Events · Cloud · Export
```

</div>

---

<div dir="rtl" align="right">

## 🗺️ نقشه فعالیت

</div>

<div dir="ltr" align="center">

```
           Farvardin            Ordibehesht            Khordad               Tir
    ┌─────────────────────┬─────────────────────┬─────────────────────┬─────────────────────┐
 Mon│ ▓ ░ ░ ▓ ░ ░ ▓ ░ ░  │ ░ ▓ ▓ ░ ░ ▓ ░ ░ ▓  │ ▓ ░ ░ ░ ▓ ▓ ░ ░ ▓  │ ░ ░ ▓ ▓ ░ ░ ░ ▓ ░  │
 Tue│ ░ ▓ ░ ░ ▓ ░ ░ ▓ ░  │ ▓ ░ ░ ▓ ░ ░ ▓ ░ ░  │ ░ ▓ ░ ▓ ░ ░ ▓ ░ ░  │ ▓ ░ ░ ░ ▓ ░ ░ ▓ ░  │
 Wed│ ░ ░ ▓ ░ ░ ▓ ░ ░ ▓  │ ░ ░ ▓ ░ ░ ▓ ░ ░ ▓  │ ░ ░ ▓ ░ ░ ▓ ░ ░ ▓  │ ░ ▓ ░ ░ ░ ▓ ░ ░ ▓  │
 Thu│ ▓ ░ ░ ▓ ░ ░ ▓ ░ ░  │ ░ ▓ ░ ░ ▓ ░ ░ ▓ ░  │ ▓ ░ ░ ▓ ░ ░ ░ ▓ ░  │ ░ ░ ▓ ░ ░ ░ ▓ ░ ░  │
 Fri│ ░ ▓ ░ ░ ▓ ░ ░ ▓ ░  │ ░ ░ ▓ ░ ░ ▓ ▓ ░ ░  │ ░ ▓ ░ ░ ▓ ▓ ░ ░ ▓  │ ░ ░ ░ ▓ ▓ ░ ░ ░ ▓  │
 Sat│ ░ ░ ▓ ░ ░ ▓ ░ ░ ▓  │ ▓ ░ ░ ▓ ░ ░ ░ ▓ ░  │ ░ ░ ▓ ░ ░ ░ ▓ ░ ░  │ ▓ ░ ░ ░ ░ ▓ ░ ░ ▓  │
 Sun│ ░ ░ ░ ▓ ░ ░ ▓ ░ ░  │ ░ ░ ▓ ░ ░ ░ ▓ ░ ░  │ ▓ ░ ░ ▓ ░ ░ ░ ▓ ░  │ ░ ▓ ░ ░ ░ ░ ▓ ░ ░  │
    └─────────────────────┴─────────────────────┴─────────────────────┴─────────────────────┘
```

</div>

<div dir="rtl" align="right">

**فرمول امتیاز هر روز:**

| شرط | امتیاز |
|---|:---:|
| روز کامل (completed) | +35 |
| دویدن ≥ ۵ دقیقه | +20 |
| فعالیت ≥ هدف | +15 |
| خواب ≥ هدف | +15 |
| (انرژی ÷ ۱۰) × ۱۵ | +15 |
| **حداکثر** | **100** |

</div>

---

<div dir="rtl" align="right">

## 📊 نمودارها

| # | نمودار | نوع | توضیح |
|---|--------|:---:|---|
| 1 | 😴 خواب | Line | روند خواب + میانگین متحرک ۷ روزه + خط هدف |
| 2 | ⚡ انرژی و حال | Line | دو محور + MA7 |
| 3 | 🏃 فعالیت | Stacked Bar | دویدن + پیاده‌روی |
| 4 | 🔥 هیت‌مپ خواب | Heatmap | ۶ سطح رنگی، شب × روز هفته |
| 5 | 🌙 ساعت خواب | Scatter | زمان خواب و بیداری |
| 6 | 📅 مقایسه هفتگی | Grouped Bar | ۵ متریک در کنار هم |
| 7 | 🕸️ رادار | Radar | ۳ هفته آخر، ۵ محور |
| 8 | 🔬 همبستگی | Scatter | خواب امشب ← → انرژی فردا |

</div>

---

<div dir="rtl" align="right">

## 🧠 تحلیل هوشمند

**Readiness Score:**

</div>

<div dir="ltr" align="center">

```
Sleep (40%)  +  Energy (35%)  +  Consistency (25%)  =  Readiness
```

</div>

<div dir="rtl" align="right">

| امتیاز | سطح | نماد |
|:---:|---|:---:|
| ۷۵+ | شدید | 💪 |
| ۵۵–۷۵ | متوسط | ⚡ |
| ۳۵–۵۵ | سبک | 🚶 |
| <۳۵ | استراحت | 🧘 |

**بهترین ساعات فعالیت:**

</div>

<div dir="rtl" align="left">

```
 🌅  5–9    │  بهترین       │  کورتیزول صبحگاهی
 ☀️  9–12   │  متوسط تا زیاد │  دمای بدن در حال افزایش
 🌞 12–15   │  احتیاط        │  گرما + افت بعد ناهار
 🌤️ 15–18   │  ⭐ اوج علمی   │  قدرت و سرعت حداکثری
 🌆 18–21   │  سبک تا متوسط  │  ریلکس شدن تدریجی
 🌙 21–24   │  استرچ         │  آماده‌سازی برای خواب
 🌃  0–3    │  تنفس ۴-۷-۸    │  تکنیک آرام‌سازی
 🌌  3–5    │  خواب          │  تلاش برای خواب عمیق
```

</div>

---

<div dir="rtl" align="right">

## ☁️ همگام‌سازی ابری

**چرا GitHub Gist؟** رایگان کامل · بدون محدودیت request · بدون فیلتر از ایران · تاریخچه تغییرات

**راه‌اندازی:** Token با scope `gist` بساز → Gist خصوصی ایجاد کن → Token + Gist ID رو تو تنظیمات بذار

</div>

<div dir="ltr" align="left">

```javascript
// Upload
PATCH https://api.github.com/gists/{gistId}
Body: { files: { "sport-data.json": { content: JSON } } }

// Download
GET https://api.github.com/gists/{gistId}
```

</div>

---

<div dir="rtl" align="right">

## 📦 مدل داده

</div>

<div dir="ltr" align="left">

```typescript
{
  rows: [{
    id: number,                // Date.now()
    jdate: string,             // "1405-04-27"
    completed: boolean,
    energy: number,            // 0–10
    mood: number,              // 0–10
    runMin: number,            // decimal: 1.17 = 1 min 10 sec
    walkMin: number,
    longestRun: number,
    activity: string,
    note: string,
    futsalRef: boolean,
    sleepBlocks: [{ label, start, end }],
    officialWake: string | null
  }],
  futsal: [{ id, jdate, energyBefore, energyAfter, durationActual, intensity }],
  goals: { sleepGoalHours: 7, activityGoalMin: 20 },
  programGoal: string
}
```

</div>

<div dir="rtl" align="right">

> ⚠️ `runMin` به صورت اعشاری ذخیره می‌شه — `formatMin()` اون رو به `mm:ss` تبدیل می‌کنه

</div>

---

<div dir="rtl" align="right">

## 🚀 نصب

</div>

<div dir="ltr" align="left">

```bash
git clone https://github.com/abbasahmadizade/my-sport-reports-only.git
cd my-sport-reports-only
open index.html
```

</div>

<div dir="rtl" align="right">

یا مستقیم از [GitHub Pages](https://abbasahmadizade.github.io/my-sport-reports-only/) استفاده کن.

</div>

---

<div dir="rtl" align="right">

## 🔧 Debug

</div>

<div dir="ltr" align="left">

```javascript
window._app.runHealthCheck()                    // health check
console.log(window._app.state().rows.length)    // row count
cloudUpload(false)                               // force sync
localStorage.removeItem('sport_auth_v1');        // reset password
localStorage.removeItem('sport_session_v1'); location.reload()
```

</div>

---

<div dir="rtl" align="right">

## 📅 Changelog

<details open>
<summary><b>v5.2</b> — ۱۴۰۵/۰۴/۲۷ (اخیر)</summary>
<br>

✨ نقشه فعالیت GitHub-style · ✨ ورود mm:ss · ✨ `formatMin()` · ⚡ مهاجرت به GitHub Gist · 🐛 CORS fix

</details>

<details>
<summary><b>v5.0</b> — ۱۴۰۵/۰۴/۲۰</summary>
<br>

✨ بازطراحی UI · ✨ ۸ نمودار · ✨ Health Check · ✨ یادآور backup

</details>

<details>
<summary><b>v4.0</b> — ۱۴۰۵/۰۳/۰۱</summary>
<br>

✨ Cloud sync · ✨ تحلیل هوشمند · ✨ Login SHA-256

</details>

</div>

---

<div dir="rtl" align="center">

### 🙏 با تشکر

[Chart.js](https://www.chartjs.org/) · [jalaali-js](https://github.com/jalaali/jalaali-js) · [Vazirmatn](https://github.com/rastikerdar/vazirmatn-font) · [GitHub Gist API](https://docs.github.com/en/rest/gists)

---

**ساخته‌شده با ❤️ در ایران**

این پروژه با کمک [**Arena.ai**](https://arena.ai) توسعه داده شده است.
<sub>کدنویسی و معماری توسط AI — نظارت و ایده‌پردازی توسط توسعه‌دهنده</sub>

[![Stars](https://img.shields.io/github/stars/abbasahmadizade/my-sport-reports-only?style=social)](https://github.com/abbasahmadizade/my-sport-reports-only/stargazers)
[![Forks](https://img.shields.io/github/forks/abbasahmadizade/my-sport-reports-only?style=social)](https://github.com/abbasahmadizade/my-sport-reports-only/network/members)

</div>
