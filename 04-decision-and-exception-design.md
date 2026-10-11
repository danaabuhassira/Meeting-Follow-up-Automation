# 04 - تصميم القرارات والاستثناءات (Decision & Exception Design)

## 1. الذكاء الاصطناعي (AI)
- يقرأ ملاحظات الاجتماع.
- يستخرج المهمة والمسؤول والموعد النهائي.
- يُعدّ ملخصًا للاجتماع.
- يحدد المعلومات الناقصة ويُرجع حالة الطلب `request_status`.

## 2. القواعد (Rules)
تستخدم **Paths by Zapier** لتحديد المسار المناسب بناءً على قيمة `request_status`:

- إذا كانت القيمة `VALID`، ينتقل الطلب إلى مسار `Valid Request`.
- إذا كانت أي قيمة أخرى، ينتقل إلى مسار `Needs Human Review`.

كما تُحدَّد قيم الحقول `requires_human` و`routing` في Google Sheets.

## 3. التدخل البشري (Human)
تتولى منظِّمة الاجتماع مراجعة الملاحظات عند نقص المعلومات، واستكمال البيانات المطلوبة، ثم إرسال رسالة المتابعة يدويًا.

## 4. حالات الاستثناء (Exceptions)

| الحالة | المثال | النتيجة |
|---|---|---|
| عدم وجود موعد نهائي | Sara will send the updated proposal to the client. | `NEEDS_REVIEW` |
| عدم تحديد المسؤول | The updated proposal must be sent to the client by Friday. | `NEEDS_REVIEW` |
| غموض الموعد | Sara will send the proposal as soon as possible. | `NEEDS_REVIEW` |
| عدم وجود مهمة واضحة | We discussed the project budget. | `NEEDS_REVIEW` |

## 5. كيفية التعامل مع الاستثناءات
لا يتوقف النظام عند وجود معلومات ناقصة، بل يحيلها إلى المراجعة البشرية. يُسجَّل صف في Google Sheets مع تعيين `requires_human = true` و`routing = human_review`، ثم يُرسَل بريد إلكتروني إلى منظِّمة الاجتماع.

## 6. الفرق بين الاستثناء والخطأ (Exception vs. Error)
- **Exception:** وجود معلومات ناقصة أو غير واضحة في الملاحظات، ويتعامل النظام معها بإحالتها إلى المراجعة.
- **Error:** حدوث خلل تقني في إحدى الأدوات، مثل AI أو Google Sheets أو Gmail، ويتطلب فحص المشكلة ومعالجتها (Debugging).
