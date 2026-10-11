# Meeting Follow-up Automation

## Problem
بعد الاجتماعات بنكتب ملاحظات بشكل حر، وكتير مرات ما بنكتب مين المسؤول عن المهمة أو متى موعدها. بتضيع المتابعة ومحدا بيعرف شو المطلوب منه.

## Solution
أتمتة على Zapier بتقرأ ملاحظات الاجتماع، وبتطلّع منها المهمة والمسؤول والموعد. إذا الثلاثة واضحين بتنبعت المتابعة للحضور، وإذا في شي ناقص بترجع للمنظّمة عشان تكمّله بنفسها.

## How it works
1. المنظّمة بتعبّي Google Form فيه ملاحظات الاجتماع.
2. Zapier بيلتقط الرد الجديد (Trigger).
3. AI by Zapier بيستخرج: action_item و owner و deadline و meeting_summary و missing_information و request_status.
4. Paths by Zapier بتفرّق حسب request_status:
   - VALID: بيتسجل صف في Google Sheets وبينبعت إيميل متابعة.
   - غير هيك (Fallback): بيتسجل صف وبينبعت إيميل للمنظّمة تراجع الملاحظات.

AI بيفهم النص، والـ Rule بتقرر المسار، والمنظّمة (إنسان) بتحكم لما المعلومات ناقصة.

## Tools Used
Google Forms, Zapier (AI by Zapier + Paths), Google Sheets, Gmail

## How to Test
1. افتحي الـ Form: [ضعي الرابط هون]
2. عبّي الاجتماع وفي Meeting Notes جرّبي واحدة من الحالات الموجودة في `06-implementation.md`.
3. اعملي Submit وانتظري دقيقة.
4. شوفي الصف الجديد في الـ Sheet: [ضعي الرابط هون] وشوفي الإيميل.

## Files
- 01-business-analysis.md
- 02-workflow-decomposition.md
- 03-data-design.md
- 04-decision-and-exception-design.md
- 05-automation-design.md
- 06-implementation.md
