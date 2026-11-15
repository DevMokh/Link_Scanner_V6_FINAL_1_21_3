# تقرير إصلاح CI — Link Scanner V6 1.21.3+27

## سبب الفشل الظاهر في الصورة
أظهر `flutter analyze` الخطأ التالي في `lib/services/network_intelligence.dart`: محاولة قراءة `network.error`، بينما النوع الداخلي `_RipeNetwork` لا يحتوي على getter باسم `error`. الحقل `error` موجود في `NetworkIntelligenceReport` فقط.

## الإصلاح
- إزالة الإسناد القديم `error: network.error` من إنشاء `NetworkIntelligenceReport`.
- الإبقاء على `NetworkIntelligenceReport.error` ليصف أخطاء الرابط غير الصالح، والوجهات المحظورة، وفشل DNS.
- إبقاء تعذّر RIPEstat الاختياري في `registrationMessage`؛ ففشل خدمة معلومات الشبكة لا يحوّل نجاح DNS إلى فشل ولا يغيّر حكم سلامة الرابط.
- رفع الإصدار إلى `1.21.3+27` وتوحيده في `pubspec.yaml` و`lib/core/app_info.dart` وREADME وسجل التغييرات.
- الاحتفاظ بإصلاحات `1.21.2+26` السابقة: تصحيح إعادة ضبط حد PhishTank في الاختبارات، والإبقاء على مؤشرات التحويلات في التقرير.

## فحوص المصدر التي أُجريت
- لا توجد مراجع متبقية لـ`network.error` أو `_RipeNetwork.error`.
- الإصدار متطابق: `pubspec.yaml` و`app_info.dart` = `1.21.3+27`.
- ملف Workflow اجتاز تحليل YAML، ويشترط نجاح التحليل والاختبارات قبل بدء البناء.
- `python -m compileall -q sandbox_service android_overlay tool` — نجح.
- `python tool/test_update_unicode_confusables.py` — نجحت 9 اختبارات.
- `python -m pytest -q sandbox_service/tests` — نجحت 27 اختبارًا.
- `bash -n` لسكريبتات التحقق والنشر — نجح.

## حدود التحقق
بيئة التحرير لا تحتوي على Flutter أو Dart SDK؛ لذلك لم يُشغّل `dart format` أو `flutter analyze` أو `flutter test` أو `flutter build apk` محليًا. فحوص Python لا تثبت نجاح كود Dart. يجب تشغيل GitHub Actions على هذه النسخة والتأكد من نجاح بوابة التحليل والاختبارات ثم البناء قبل اعتماد APK.
