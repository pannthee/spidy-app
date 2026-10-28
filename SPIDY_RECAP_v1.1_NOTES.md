# Spidy Recap v1.1 — Notes (2026-10-28)

Built from the user's OWN Spidy / One Click Recap codebase (`~/workspace/spidy-app`,
`com.spidy.studio`). Zippy Cut v1.0.2 APK was inspected at STRUCTURAL level only
(file list / sizes / asset names) to understand the functional flow. NO Zippy code,
HTML, JS, images, poster designs, prompts or branding were copied, extracted or
reused. There is no login key / trial / auth in Spidy — it is the user's own app.

## Flow (Zippy-style functional flow, Spidy implementation)
1. Video ရွေးပါ → duration သိတာနဲ့ 60-second Rolls အလိုအလို ပေါ်လာတယ်
2. Mode ရွေးပါ (Recap / Subtitle / Dubbing / Recap+Dub) — အရင်အတိုင်း
3. Generate Script — IN-APP Gemini only (external Gemini app ကို copy/paste မလုပ်ဘူး).
   Script ကို Roll တစ်ခုချင်းစီမှာ ခွဲပြတယ်။
4. Timeline / Rolls — Roll တစ်ခုချင်းစီကို:
   - Narration ပြင်မယ် / Dialogue ပြင်မယ်
   - ထားမယ် / ဖြတ်မယ် (ဖြတ်ထားတဲ့ Roll က အသံ + Render မှာ မပါဘူး)
   - အချိန်ကို နှိပ်ပြီး video ကြည့်မယ်
   - Roll တစ်ခုတည်း အသစ်ထုတ်မယ် (အဲဒီ Roll ရဲ့ audio + frames နဲ့ပဲ Gemini ပြန်ခေါ်တယ်)
   - Roll ပြင်တိုင်း Script + Scenes နဲ့ sync ဖြစ်တယ် (အသံ ပြန်ထုတ်ရမယ်)
5. Project / Series — နာမည် + Ep. သိမ်းမယ်, ပြန်ဖွင့်မယ်, ဖျက်မယ်
   (localStorage `spidy_projects`, အများဆုံး 20 ခု။ Video ဖိုင်ကိုယ်တိုင် မသိမ်းဘူး —
   ပြန်ဖွင့်ရင် video ပြန်ရွေးရမယ်။)
6. အသံ (Gemini TTS / Edge TTS Nilar+Thiha+Multilingual) → Preview → Render
   (canvas + MediaRecorder, အရင်အတိုင်း) → Documents/Spidy/ ထဲ download

## Branding
- App name: Spidy / Spidy Recap, package `com.spidy.studio`, key `spidy_gemini_api_key`
- Logo #3 (violet-orange) အရင်အတိုင်း
- UPDATE 2026-10-28 (user request, Spidy only): "Create by Bo Bo Myint Mo" credit ကို
  Spidy app UI (API key modal + footer) ကနေ အပြီးဖြုတ်ပြီးပြီ။ Render ထဲမှာလည်း
  credit/watermark ဘာမှ မပါဘူး (subtitle + user logo ပဲ)။ တခြား app တွေက credit
  တွေကို မထိထားဘူး။

## Render — local phone only
- Render က ဖုန်းထဲမှာပဲ: canvas + MediaRecorder + AudioContext, WebView/Capacitor
  ထဲမှာ တိုက်ရိုက်လုပ်တယ်။ Video blob ကို server/cloud ကို upload လုပ်တဲ့ code လုံးဝ
  မရှိဘူး (network calls ၂ ခုပဲ: Gemini generateContent + Edge voices list)။
- Render ပြီးရင် Capacitor Filesystem နဲ့ Documents/Spidy/ ထဲ တိုက်ရိုက်သိမ်းတယ်။
- Script/အသံထုတ်တာပဲ Gemini/Edge API (cloud) ကို သုံးတယ် — အဲဒါမပါရင် AI script/TTS မရဘူး။

## What is NOT in this build (on purpose)
- Native FFmpeg render (Zippy လို 75MB native FFmpegKit မပါ) — render က browser/Canvas
  MediaRecorder နဲ့ပဲ, WebM ထွက်နိုင်တယ်။
- Poster design cards (Zippy ရဲ့ 33 WebP designs ကို copy မလုပ်ဘူး)
- Preset sharing ("school") / chat panel / voicer panel — မပါ
- Login key / device binding — မပါ (ကိုယ်ပိုင် app မို့ မလိုဘူး)

## APK route — local build is impossible (known sandbox limit)
JVM TCP blackhole ကြောင့် ဒီ VM မှာ Gradle APK build မရဘူး (2026-10-22 verified)။
`.github/workflows/build-apk.yml` အဆင်သင့်ရှိနေတယ်။
User လုပ်ရမှာ: GitHub မှာ empty PUBLIC repo တစ်ခု ဖန်တီးပါ (ဥပမာ `spidy-recap-app`,
README/.gitignore/license မထည့်ဘဲ), ပြီးရင် Muse က push လုပ်ပြီး Actions က APK build မယ်။
UPDATE 2026-10-28: `create_repository` ကို connector ကနေ ထပ်စမ်းကြည့်ပြီးပြီ —
403 "Resource not accessible by integration" နဲ့ ထပ်ကျတယ် (တတိယအကြိမ်)၊
repo ကို Muse က ဖန်တီးလို့မရဘူး။ Workflow ကို ပြင်ထားပြီ: repo ထဲကို binary
ဖိုင်တွေ (launcher/splash PNG, logo.png, gradle-wrapper.jar) ထည့်လို့မရတာကြောင့်,
Actions runner ပေါ်မှာ `npx cap add android` နဲ့ Android project ကို အသစ်
scaffold လုပ်ပြီး sync/build လုပ်မယ် (MainActivity package လည်း
com.spidy.studio အမှန်ဖြစ်မယ်)။ Runner ပေါ်မှာ logo.png မရှိရင် violet
placeholder ထုတ်သုံးမယ် — အဲဒါကြောင့် ပထမ APK ရဲ့ header logo က အရောင်တူ
တုံးပဲ ဖြစ်နိုင်တယ်၊ တကယ့် Spidy logo ပြန်ထည့်ချင်ရင် GitHub web ကနေ
public/logo.png ကို upload လုပ်ပြီး rebuild လုပ်ရမယ်။

## On-phone test checklist
1. Video ရွေးပါ → Rolls ပေါ်လား (ဥပမာ 2:30 video = 3 rolls)
2. Generate Script → Roll တစ်ခုချင်းစီမှာ စာသား ရောက်လား
3. Roll တစ်ခု ဖြတ်မယ် → အသံထုတ်ရင် ဖြတ်ထားတာ မပါလား
4. Roll တစ်ခု အသစ်ထုတ်မယ် → အဲဒီ Roll ပဲ ပြောင်းလား
5. Project သိမ်းမယ် → app ပြန်ဖွင့်ပြီး ပြန်ခေါ်လား (video ပြန်ရွေးဖို့ သတိပေးလား)
6. Render → Documents/Spidy/ ထဲ video ရောက်လား
