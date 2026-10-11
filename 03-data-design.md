# 03 - Data Design

## Trigger
Google Forms: New Form Response.

## Inputs (من الـ Form)
- Meeting Title
- Organizer Name
- Organizer Email
- Attendees Email
- Meeting Date
- Meeting Notes (Paragraph)

## Unstructured Data
Meeting Notes، لأن المنظّمة بتكتبها بحرية. مثال:
`Sara will send the updated proposal to the client by Friday.`

## Structured Data (ناتج AI)
- action_item: المهمة
- owner: المسؤول
- deadline: الموعد
- meeting_summary: ملخص قصير
- missing_information: اسم الحقل الناقص، أو NONE إذا ما في نقص
- request_status: VALID أو NEEDS_REVIEW

## Data Schema (أعمدة الـ Sheet)
meeting_title, organizer_name, organizer_email, attendees_email, meeting_date, meeting_notes, action_item, owner, deadline, meeting_summary, missing_information, request_status, requires_human, routing, processed_at

## Missing Information
الحقول المطلوبة هي action_item و owner و deadline. أي حقل منهم مش مذكور بشكل صريح أو غامض بنعتبره ناقص.

## Validation
- ما بنقبل "we" أو "the team" كمسؤول.
- الموعد الغامض (soon, as soon as possible, later) بنعتبره ناقص.
- AI ممنوع يخمّن.
- VALID بس إذا الحقول الثلاثة موجودة وواضحة.

## ملاحظة عن القيمة الفارغة
كان تصميمي إن الحقل الناقص يرجع بكلمة NOT_PROVIDED. بالتنفيذ الفعلي الحقل الناقص بيطلع فاضي في الـ Sheet. التفاصيل في `06-implementation.md`.
