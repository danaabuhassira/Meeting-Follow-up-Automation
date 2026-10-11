# 06 - Implementation

هاد اللي بنيته فعليًا على Zapier.

## الأدوات
- Google Form: Meeting Follow-up Form. الرابط: https://forms.gle/2kLSZj3ZvfAJ6AAZ6
- Google Sheet: Meeting Follow-up Records (15 عمود). الرابط: https://docs.google.com/spreadsheets/d/1aN6CBIR3JCemOshwFxCPm51Av_Dl1n54R1WqvoJMIL4/edit?usp=sharing
- Zap اسمه: AI Meeting Follow-up & Routing


## خطوات الـ Zap
1. Google Forms - New Form Response
2. AI by Zapier - Extract follow-up action from meeting notes. حطيت Meeting Notes جوا الـ Prompt مباشرة، لأن الواجهة الجديدة ما فيها Data In منفصل. الـ Output Fields الستة من نوع Text.
3. Paths by Zapier
   - Valid Request: request_status Exactly matches VALID
     - Google Sheets - Create Spreadsheet Row (requires_human = false, routing = send_followup)
     - Gmail - Send Email
   - Fallback (Needs Human Review)
     - Google Sheets - Create Spreadsheet Row (requires_human = true, routing = human_review)
     - Gmail - Send Email

## الاختبارات
كل اختبار عملته من الـ Form بعد ما نشرت الـ Zap (Publish).

| Test | Meeting Notes | Expected | Actual status | Result |
|---|---|---|---|---|
| 01 | Sara will send the updated proposal to the client by Friday. | VALID | VALID, missing = NONE | PASS |
| 02 | Sara will send the updated proposal to the client. | NEEDS_REVIEW | NEEDS_REVIEW, missing = deadline | PASS |
| 03 | The updated proposal must be sent to the client by Friday. | NEEDS_REVIEW | NEEDS_REVIEW, missing = owner | PASS |
| 04 | Sara will send the proposal as soon as possible. | NEEDS_REVIEW | NEEDS_REVIEW, missing = deadline | PASS |
| 05 | We discussed the project budget. | NEEDS_REVIEW | NEEDS_REVIEW, missing = action_item, owner, deadline | PASS |

في اختبار 04 قالت الجملة "as soon as possible" وAI ما حوّلها لموعد، فضل الموعد فاضي وطلع NEEDS_REVIEW.

## المشاكل اللي واجهتها

1. **اخترت Clear بدل Create:** في الـ Fallback path اخترت بالغلط Event اسمه Clear Spreadsheet Row(s) بدل Create Spreadsheet Row. هاد كان بدو يمسح صفوف من الـ Sheet مش يضيف صف. لاحظت إنه طالب حقل Row(s) وهاد مش موجود في Create، فرجعت لتبويب Setup وغيّرت الـ Event لـ Create Spreadsheet Row.

2. **القيمة الفارغة بدل NOT_PROVIDED:** كان تصميمي إن الحقل الناقص (action_item أو owner أو deadline) يرجع بكلمة NOT_PROVIDED، وعدّلت الـ Prompt على هالأساس. بس بالتنفيذ الفعلي لسا بيطلع الحقل فاضي في الـ Sheet. هاي مشكلة لسا ما انحلّت. تأثيرها محدود: missing_information و request_status بيطلعوا صح، فالقرار والمسار سليمين، والخلية الفاضية بس شكلها مش واضح. الحل اللي لسا بدي أجربه هو تعديل إعدادات الـ Output Fields أو إضافة خطوة تحويل القيمة الفاضية.

3. **missing_information كان بيطلع فاضي:** بعد ما أطفيت Required عن الحقول، صار هالحقل بيطلع فاضي أحيانًا. انحلت بعد تعديل إعدادات الـ Output Fields وإعادة الاختبار، وهلأ بيطلع صح في كل الاختبارات.

4. **Required في Output Fields:** أطفيت Required عن action_item و owner و deadline عشان AI ما يضطر يخترع قيمة لو المعلومة مش موجودة.

5. **processed_at:** ما لقيت Zap Meta في الواجهة، فاستخدمت Create Time من الـ Trigger. هاد الوقت بتوقيت UTC.

6. **إيميل التجربة:** وقت الاختبار كان حقل To هو إيميلي الشخصي.

## اللي تعلمته
لما شي ما اشتغل، ما غيّرت كل شي عشوائي. دوّرت على أول مكان طلعت فيه نتيجة غلط (AI، ولا Path، ولا Mapping، ولا Email)، وهيك لقيت إن مشكلة Clear كانت في اختيار الـ Event مش في البيانات.
