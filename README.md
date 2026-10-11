# Meeting Follow-up Automation

## 1. المشكلة (Problem)

بعد الاجتماعات، تُدوَّن الملاحظات غالبًا بصورة حرة وغير منظمة، وقد لا يُحدَّد الشخص المسؤول عن كل مهمة أو موعد إنجازها. يؤدي ذلك إلى صعوبة متابعة المهام وعدم وضوح المطلوب من كل شخص.

## 2. الحل (Solution)

أتمتة باستخدام Zapier لمعالجة ملاحظات الاجتماع واستخراج المهمة والمسؤول والموعد النهائي. إذا كانت المعلومات الثلاثة واضحة، تُرسَل رسالة المتابعة إلى الحضور. أما إذا كانت هناك معلومات ناقصة، فتُحال الملاحظات إلى منظِّمة الاجتماع لاستكمالها يدويًا.

## 3. آلية العمل (How It Works)

1. تُدخل منظِّمة الاجتماع الملاحظات عبر Google Form.
2. يلتقط Zapier الاستجابة الجديدة (Trigger).
3. يستخرج AI by Zapier المعلومات التالية:
   - `action_item`: المهمة المطلوبة.
   - `owner`: الشخص المسؤول.
   - `deadline`: الموعد النهائي.
   - `meeting_summary`: ملخص الاجتماع.
   - `missing_information`: المعلومات الناقصة.
   - `request_status`: حالة الطلب.
4. يستخدم Paths by Zapier حالة الطلب لتحديد المسار المناسب:
   - **VALID:** يُسجَّل صف في Google Sheets، وتُرسَل رسالة متابعة عبر البريد الإلكتروني.
   - **Fallback:** يُسجَّل صف في Google Sheets، وتُرسَل رسالة إلى منظِّمة الاجتماع لمراجعة الملاحظات واستكمال المعلومات.

يعتمد النظام على الذكاء الاصطناعي لفهم النص واستخراج المعلومات، وعلى القواعد لتحديد المسار، وعلى التدخل البشري لمعالجة المعلومات الناقصة.

## 4. الأدوات المستخدمة (Tools Used)

- Google Forms
- Zapier: AI by Zapier وPaths by Zapier
- Google Sheets
- Gmail

## 5. كيفية الاختبار (How to Test)

1. افتحي النموذج: [Meeting Follow-up Form](https://forms.gle/2kLSZj3ZvfAJ6AAZ6).
2. أدخلي بيانات الاجتماع، وجرّبي إحدى الحالات الموجودة في الملف `06-implementation.md`.
3. اضغطي على Submit وانتظري حتى تُنفَّذ الأتمتة.
4. تحققي من تسجيل البيانات في [Google Sheets](https://docs.google.com/spreadsheets/d/1aN6CBIR3JCemOshwFxCPm51Av_Dl1n54R1WqvoJMIL4/edit?usp=sharing)، ثم تحققي من وصول رسالة البريد الإلكتروني.

## 6. ملفات المشروع (Project Files)

- `01-business-analysis.md` — تحليل الأعمال.
- `02-workflow-decomposition.md` — تفكيك سير العمل.
- `03-data-design.md` — تصميم البيانات.
- `04-decision-and-exception-design.md` — تصميم القرارات والاستثناءات.
- `05-automation-design.md` — تصميم الأتمتة.
- `06-implementation.md` — التنفيذ والاختبارات.
