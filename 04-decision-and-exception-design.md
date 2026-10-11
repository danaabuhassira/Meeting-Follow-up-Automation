# 04 - Decision & Exception Design

## AI (بيفهم)
- بيقرأ الملاحظات الحرة.
- بيستخرج المهمة والمسؤول والموعد.
- بيكتب الملخص.
- بيحدد الناقص وبيعطي request_status.

## Rule (بتتحكم)
Paths by Zapier بتفحص request_status:
- بيساوي VALID بالضبط: مسار Valid Request.
- أي شي غير هيك: مسار Fallback (Needs Human Review).
وكمان القيم الثابتة في الـ Sheet: requires_human و routing.

## Human (بيحكم)
منظّمة الاجتماع. لما المعلومات ناقصة بتوصلها الملاحظات الأصلية والشي الناقص، وبتكمّل وبتبعت المتابعة بنفسها.

## Exceptions
| الحالة | مثال | النتيجة |
|---|---|---|
| ما في موعد | Sara will send the updated proposal to the client. | NEEDS_REVIEW |
| ما في مسؤول | The updated proposal must be sent to the client by Friday. | NEEDS_REVIEW |
| موعد غامض | Sara will send the proposal as soon as possible. | NEEDS_REVIEW |
| ما في مهمة | We discussed the project budget. | NEEDS_REVIEW |

## شو بصير لما تظهر Exception
ما بنوقفها، بنوجّهها. بينسجل صف مع requires_human = true و routing = human_review، وبينبعت إيميل للمنظّمة.

## Exception وError مش نفس الشي
- Exception: الملاحظات ناقصة. هاد شي طبيعي والنظام بتعامل معه.
- Error: الأداة نفسها فشلت (AI أو Sheets أو Gmail). هاد بيحتاج Debugging.
