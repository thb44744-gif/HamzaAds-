# حمزة للإعلانات

منصة إعلانات محلية بالعربية (RTL) بأسلوب حديث مع وضع ليلي. تتيح لأي مستخدم نشر إعلان مجانًا أو عبر خطط مدفوعة قابلة للتعديل من لوحة المالك، وتدرّ دخلها عن طريق **AdMob الحقيقي** (لا إعلانات وهمية ولا أرباح مصطنعة).

## الميزات
- نشر/تعديل/حذف إعلانات مع صور (حتى 6) وفيديو اختياري.
- إعلانات: مجانية، مميزة، مثبتة، عاجلة، وصول أكبر (تُدار الأسعار والمدة من لوحة المالك).
- نظام مشاهدات مدرّة للدخل: تنعكس الأرباح الفعلية من شبكة AdMob في إحصائية منظمة (ظهور اليوم/الشهر/الإجمالي).
- دفع حقيقي عبر واجهة `PaymentGateway` قابلة للربط (لا يوجد دفع وهمي).
- لوحة تحكم مالك: إحصائيات، مراجعة الإعلانات، إدارة المستخدمين، الأسعار، وسجل الإيرادات.
- إشعارات عبر Firebase Cloud Messaging.
- عربي RTL + وضع ليلي + شعار «حمزة».

## التشغيل
```
./gradlew assembleDebug   # ضمن Android Studio أو CLI
```
الخروج عند النجاح: `app/build/outputs/apk/debug/app-debug.apk`.

## الإعداد قبل النشر (خطوات حقيقية تُنفذ يدويًا)

1. **Firebase**:
   - أنشئ مشروعًا في Firebase Console وأضف تطبيق Android بحزمة `com.hamza.ads`.
   - حمّل `google-services.json` وحل محل القالب في `app/google-services.json`.
   - وفّع Authentication (بريد/كلمة مرور) و Firestore و Cloud Messaging.
   - انشر قواعد الأمان من `firestore.rules`.

2. **AdMob (الدخل الفعلي)**:
   - أنشئ حساب AdMob، وأبقِ `values/admob.xml` معطّلًا (`admob_enabled=false`) وكل المعرفات فارغة في البداية.
   - املأ مرة واحدة في `values/admob.xml`:
     - `admob_app_id` (المعرّف من قسم App settings في AdMob).
     - `admob_banner_id` / `admob_native_id` / `admob_interstitial_id` / `admob_rewarded_id`.
     - `admob_est_ecpm_*`: معدلات eCPM تقديرية إرشادية فقط لتقديم رقم «تقديري»، والأرقام الدقيقة من لوحة AdMob الرسمية.
   - في التطوير يمكن إبقاء `admob_use_test_ads=true` (استخدام المعرفات الرسمية للاختبار فقط، لا توجد معرفات وهمية كحقيقية).

3. **بوابة الدفع**:
   - الربط عبر واجهة `data/util/PaymentGateway.kt`. التنفيذ الحالي `MockPaymentGateway` يوضح الطريق فقط ولا يُسوَّق كحل نهائي.
   - يبقى أداء أي عملية دفع حقيقة (معلق/ناجح/فاشل) عبر تطبيق مباشر مع بوابة معتمدة قبل النشر.

4. **لوحة المالك**: أحدّث صلاحية `isAdmin` لأي مستخدم في Firestore بمجموعة `users/{uid}`، ومن ثم يظهر زر الإحصائيات في أعلى الشاشة.

## هيكل الكود
- `data/model` — نماذج: `UserAd`, `AppUser`, `AppSettings`, `Payment`, `Category`, `AppNotification`, `Promotion`, `ViewEvent`, `AdEvent`.
- `data/repository` — `AdsRepository`, `AuthRepository`, `AdminRepository`, `PaymentRepository`, `NotificationRepository`, `PromiseRepository`, `CategoryRepository`, `AdStatsRepository`.
- `data/ad/AdManager.kt` — متحكم AdMob (banner/native/interstitial/rewarded) مع تحميل تلقائي وإعادة محاولة متدرجة.
- `ui` — شاشات المستخدم ولوحة المالك مع ViewModels مستقلة.
- `res/values/admob.xml` — ملف الإعداد الوحيد لمعرّفات AdMob.