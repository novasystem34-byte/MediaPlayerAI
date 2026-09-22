# MediaPlayerAI — مشغل وسائط ذكي (C# / WPF)

مشغل وسائط احترافي مبني بـ WPF (.NET 8) يدعم تشغيل الصوت والفيديو، مع تكامل ذكاء اصطناعي
عبر [Groq](https://console.groq.com/keys)، وإمكانية تسجيله كخيار مشغل افتراضي في ويندوز.

## المتطلبات

- Windows 10 أو 11
- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- Visual Studio 2022 (اختياري، لفتح ملف `MediaPlayerAI.sln`)
- مفتاح Groq API مجاني من: https://console.groq.com/keys

## التشغيل

### من Visual Studio
1. افتح `MediaPlayerAI.sln`.
2. اضغط F5 للتشغيل.

### من سطر الأوامر
```bash
cd MediaPlayerAI
dotnet restore
dotnet run
```

### لإنتاج ملف تنفيذي (exe) جاهز للتوزيع
```bash
dotnet publish -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true
```
سيظهر الملف التنفيذي داخل: `bin\Release\net8.0-windows\win-x64\publish\`

## المميزات

- تشغيل صوت وفيديو (MP4, MKV, AVI, MP3, FLAC, WAV، وغيرها) عبر `MediaElement`.
- قائمة تشغيل، سحب وإفلات، تكرار وعشوائي، تحكم بالصوت والتقديم/التأخير.
- **مساعد ذكاء اصطناعي** (Groq) للدردشة والسؤال داخل المشغل.
- **تفريغ صوتي إلى نص** (Groq Whisper Large v3 Turbo) للملف الحالي.
- **إعدادات API** لحفظ مفتاح Groq **مشفّراً** على القرص باستخدام Windows DPAPI.
- **تسجيل كمشغل افتراضي** في نظام ويندوز.
- **اتفاقية ترخيص (EULA)** تظهر أول مرة يشتغل فيها البرنامج، باسم NOVA SYSTEM، ولازم المستخدم
  يوافق عليها (بعد قراءتها كاملة) قبل ما يدخل للمشغل. يمكن مراجعتها لاحقاً من زر
  "📜 شروط الاستخدام" داخل نافذة الإعدادات.
- **مثبّت Windows احترافي** (Inno Setup) بصفحة ترخيص رسمية، موجود داخل مجلد `installer/`.

## بناء المثبّت (Setup.exe) خطوة بخطوة

1. **ابنِ نسخة مستقلة (self-contained)** من داخل مجلد `MediaPlayerAI/MediaPlayerAI`:
   ```bash
   dotnet publish -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true -p:IncludeNativeLibrariesForSelfExtract=true
   ```
   سينتج الملف داخل: `MediaPlayerAI\bin\Release\net8.0-windows\win-x64\publish\`

2. **ثبّت Inno Setup** (مجاني): https://jrsoftware.org/isdl.php

3. افتح ملف `installer\setup.iss` داخل برنامج **Inno Setup Compiler**، واضغط **Build > Compile**
   (أو F9).

4. سيظهر المثبّت الجاهز داخل: `installer\Output\MediaPlayerAI_Setup.exe`

عند تشغيل هذا المثبّت، سيعرض على المستخدم صفحة ترخيص رسمية (نفس نص `LICENSE.txt`) يجب الموافقة
عليها قبل إكمال التثبيت — بالإضافة إلى نافذة الترخيص الداخلية التي تظهر أول مرة يفتح فيها البرنامج
نفسه (حماية مزدوجة: عند التثبيت وعند أول تشغيل).

> **تنبيه قانوني:** نص `LICENSE.txt` المرفق هو **قالب عام** لأغراض توضيح الفكرة، وليس نصاً قانونياً
> جاهزاً للاستخدام التجاري. راجعه مع محامٍ أو مستشار قانوني مختص قبل توزيع البرنامج فعلياً، خصوصاً
> إذا كنت ستوزّعه تجارياً أو في بلدان لها قوانين حماية مستهلك محددة.

## ⚠️ ملاحظة مهمة بخصوص "التعيين التلقائي كمشغل افتراضي"

منذ Windows 8، لا يسمح النظام لأي برنامج بتغيير الارتباطات الافتراضية للملفات **بصمت ودون علم المستخدم**،
وذلك لمنع البرامج الخبيثة من الاستيلاء على هذه الإعدادات. هذا قيد أمني من مايكروسوفت نفسها ولا يمكن تجاوزه برمجياً
(ولا حتى بصلاحيات المدير). لذلك فإن زر **"تعيين كمشغل افتراضي"** في التطبيق يقوم بأقصى ما هو ممكن:

1. يسجّل التطبيق في الريجستري (`HKEY_CURRENT_USER`) ليظهر كخيار متاح لكل امتدادات الصوت والفيديو.
2. يفتح صفحة **الإعدادات > التطبيقات > التطبيقات الافتراضية** في ويندوز مباشرة، حيث يختار المستخدم
   `MediaPlayerAI` بنقرة واحدة لكل امتداد يريده (أو "تعيين افتراضي حسب التطبيق").

## بنية المشروع

```
MediaPlayerAI/
├── MediaPlayerAI.sln
└── MediaPlayerAI/
    ├── MediaPlayerAI.csproj
    ├── app.manifest
    ├── App.xaml / App.xaml.cs
    ├── MainWindow.xaml / MainWindow.xaml.cs      # الواجهة الرئيسية للمشغل
    ├── SettingsWindow.xaml / SettingsWindow.xaml.cs  # إعدادات مفتاح Groq
    ├── Converters/
    │   └── TimeSpanToStringConverter.cs
    └── Services/
        ├── ApiKeyManager.cs             # تشفير/فك تشفير المفتاح (DPAPI)
        ├── GroqService.cs               # الاتصال بـ Groq API (محادثة + Whisper)
        └── FileAssociationManager.cs    # تسجيل الامتدادات + فتح إعدادات ويندوز
```

## ملاحظة حول الترميزات (Codecs)

`MediaElement` يعتمد على Windows Media Foundation المثبت على النظام. بعض ترميزات الفيديو النادرة
(مثل بعض ملفات MKV/HEVC القديمة) قد لا تعمل افتراضياً. لدعم كل الترميزات دون استثناء، يمكن لاحقاً
استبدال `MediaElement` بمكتبة `LibVLCSharp` (تعتمد على محرك VLC) — البنية الحالية للكود (كل التحكم
معزول داخل `MainWindow.xaml.cs`) تسهّل هذا الاستبدال مستقبلاً دون إعادة كتابة الواجهة.
