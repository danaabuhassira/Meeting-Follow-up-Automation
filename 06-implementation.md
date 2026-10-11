# 06 - التنفيذ (Implementation)

يوثّق هذا القسم ما تم بناؤه فعليًا باستخدام Zapier، والخطوات التي نُفِّذت، ونتائج الاختبارات، والمشكلات التي ظهرت أثناء التنفيذ وكيفية التعامل معها.

## 1. الأدوات المستخدمة

- **Google Forms:** نموذج `Meeting Follow-up Form`  https://forms.gle/2kLSZj3ZvfAJ6AAZ6
- **Google Sheets:** جدول `Meeting Follow-up Records`، ويحتوي على 15 عمودًا.  
 https://docs.google.com/spreadsheets/d/1aN6CBIR3JCemOshwFxCPm51Av_Dl1n54R1WqvoJMIL4/edit?usp=sharing
- **Zapier:** أتمتة باسم `AI Meeting Follow-up & Routing`.

## 2. خطوات الأتمتة (Zap Workflow)

### الخطوة الأولى: استقبال الرد
**Google Forms — New Form Response**

يبدأ التشغيل عند وصول استجابة جديدة من النموذج.

### الخطوة الثانية: استخراج المعلومات باستخدام الذكاء الاصطناعي
**AI by Zapier — Extract follow-up action from meeting notes**

أُدرجت ملاحظات الاجتماع مباشرةً داخل الـ Prompt، لأن الواجهة المستخدمة لا توفر حقلًا منفصلًا باسم `Data In`. كما أُعدّت ستة حقول للمخرجات، جميعها من نوع `Text`.

### الخطوة الثالثة: تحديد المسار
**Paths by Zapier**

يحتوي الـ Zap على مسارين:

- **Valid Request:** يُختار عندما تطابق قيمة `request_status` النص `VALID` تمامًا.
  - تسجيل البيانات في Google Sheets باستخدام `Create Spreadsheet Row`، مع تعيين `requires_human = false` و`routing = send_followup`.
  - إرسال رسالة بريد إلكتروني باستخدام Gmail.

- **Fallback (Needs Human Review):** يُستخدم عندما لا تطابق الحالة القيمة `VALID`.
  - تسجيل البيانات في Google Sheets باستخدام `Create Spreadsheet Row`، مع تعيين `requires_human = true` و`routing = human_review`.
  - إرسال رسالة بريد إلكتروني إلى منظِّمة الاجتماع لمراجعة المعلومات الناقصة.

## 3. الاختبارات ونتائجها

نُفِّذت الاختبارات الخمسة من خلال النموذج بعد نشر الـ Zap باستخدام `Publish`.

| الاختبار | ملاحظات الاجتماع | النتيجة المتوقعة | النتيجة الفعلية | الحالة |
|---|---|---|---|---|
| 01 | Sara will send the updated proposal to the client by Friday. | `VALID` | `VALID`، والمعلومات الناقصة: `NONE` | PASS |
| 02 | Sara will send the updated proposal to the client. | `NEEDS_REVIEW` | `NEEDS_REVIEW`، والموعد النهائي مفقود | PASS |
| 03 | The updated proposal must be sent to the client by Friday. | `NEEDS_REVIEW` | `NEEDS_REVIEW`، والمسؤول مفقود | PASS |
| 04 | Sara will send the proposal as soon as possible. | `NEEDS_REVIEW` | `NEEDS_REVIEW`، والموعد النهائي مفقود | PASS |
| 05 | We discussed the project budget. | `NEEDS_REVIEW` | `NEEDS_REVIEW`، والمهمة والمسؤول والموعد النهائي مفقودة | PASS |

في الاختبار الرابع، لم يتعامل الذكاء الاصطناعي مع عبارة `as soon as possible` باعتبارها موعدًا نهائيًا محددًا، بل ترك الموعد فارغًا وصنّف الحالة على أنها `NEEDS_REVIEW`.

**نتيجة الاختبارات:** اجتازت الحالات الخمس الاختبارات وفق النتائج المتوقعة.

## 4. المشكلات التي واجهتني أثناء التنفيذ

### 4.1 اختيار حدث غير صحيح في Google Sheets

في مسار `Fallback`، اخترت بالخطأ الحدث `Clear Spreadsheet Row(s)` بدلًا من `Create Spreadsheet Row`. كان الحدث الأول مخصصًا لمسح الصفوف، وليس إضافة صف جديد.

لاحظت وجود حقل `Row(s)`، وهو ما ساعدني على اكتشاف الاختيار غير الصحيح. بعد ذلك، عدت إلى تبويب `Setup` وغيّرت الحدث إلى `Create Spreadsheet Row`.

### 4.2 ظهور القيم الناقصة فارغة بدلًا من `NOT_PROVIDED`

كان التصميم الأولي يقتضي أن يُرجع الذكاء الاصطناعي القيمة `NOT_PROVIDED` عند غياب معلومات مثل `action_item` أو `owner` أو `deadline`. وعدّلت الـ Prompt لتحقيق ذلك، لكن الحقول ظلت تظهر فارغة في Google Sheets.

لم تُحل هذه المشكلة بعد. ومع ذلك، فإن تأثيرها محدود؛ لأن حقلي `missing_information` و`request_status` يعرضان المعلومات اللازمة لاتخاذ القرار وتحديد المسار الصحيح.

**الحل المقترح للتجربة لاحقًا:** تعديل إعدادات `Output Fields` أو إضافة خطوة لتحويل القيم الفارغة إلى `NOT_PROVIDED`.

### 4.3 ظهور حقل `missing_information` فارغًا

كان هذا الحقل يظهر فارغًا في بعض الاختبارات بعد إيقاف خيار `Required` عن الحقول. عولجت المشكلة بتعديل إعدادات `Output Fields` وإعادة الاختبار، وأصبح الحقل يعرض المعلومات الناقصة بصورة صحيحة في الاختبارات المنفَّذة.

### 4.4 إعداد الحقول المطلوبة

أوقفت خيار `Required` عن الحقول `action_item` و`owner` و`deadline`، حتى لا يُضطر الذكاء الاصطناعي إلى اختلاق قيم عندما لا تتوفر المعلومات في ملاحظات الاجتماع.

### 4.5 تسجيل وقت المعالجة

لم أجد خيار `Zap Meta` في الواجهة المستخدمة، لذلك استخدمت حقل `Create Time` من المشغّل لتسجيل وقت إنشاء الاستجابة. يُسجَّل هذا الوقت وفق توقيت UTC.

### 4.6 استخدام البريد الشخصي أثناء الاختبار

استخدمت بريدي الإلكتروني الشخصي في حقل `To` أثناء اختبار إرسال الرسائل.


وقد ساعدني هذا الأسلوب على اكتشاف أن مشكلة مسح الصفوف كانت ناتجة عن اختيار الحدث غير الصحيح في Google Sheets، وليس عن البيانات نفسها.

كما تعلمت أن نجاح الأتمتة لا يعتمد فقط على تنفيذ الخطوات، بل يتطلب اختبار الحالات المختلفة، وتوثيق المشكلات التي لم تُحل بعد، والتمييز بين ما يعمل فعليًا وما يزال بحاجة إلى تحسين.
