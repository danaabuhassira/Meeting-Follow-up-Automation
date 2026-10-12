# 05 - Automation Design

صممت الـ Workflow قبل ما أبني.

## Workflow
```
Google Form
   |
Trigger: New Form Response
   |
AI by Zapier (Meeting Notes)
   |
Paths by Zapier (request_status)
   |-- VALID --> Google Sheets (requires_human=false, routing=send_followup) --> Gmail للحضور
   |-- Fallback --> Google Sheets (requires_human=true, routing=human_review) --> Gmail للمنظّمة
```

##سبب كل خطوة
- Google Form: مصدر بيانات واحد ونقطة دخول واضحة.
- Trigger: بيبدأ الأتمتة مع كل رد جديد.
- AI: بيحوّل النص الحر لبيانات منظمة.
- request_status: إشارة وحدة واضحة بسهل أختبرها بدل ما الـ Path يفسّر عدة حقول.
- Paths مع Fallback: لأني بدي أوجّه الطلب الناقص مش أوقفه.
- Google Sheets: سجل للتتبع والاختبار.
- Gmail: بنبلّغ الشخص المناسب.


## AI Prompt
```
Analyze the meeting notes and extract the main follow-up action.

Use ONLY information explicitly provided in the notes. Never invent, assume, infer, estimate, or guess.

Required information:
- action_item
- owner
- deadline

Extraction rules:

action_item
- Extract the main task explicitly agreed in the meeting.
- If no clear task is stated, return NOT_PROVIDED.

owner
- Extract the person explicitly responsible for the task (a name).
- If not explicitly stated, return NOT_PROVIDED.
- Words like "we", "the team", "someone" are NOT a specific owner, so return NOT_PROVIDED.

deadline
- Extract a specific timeframe exactly as stated.
- Valid examples: "by Friday", "next week", "in 3 days", "by December 5".
- Vague expressions such as "soon", "as soon as possible", "later", "when possible" -> return NOT_PROVIDED.
- Never convert a vague timeframe into a specific one.

meeting_summary
- Summarize only what was explicitly said. Do not add assumptions.

missing_information
- List required fields that are missing or ambiguous.
- Allowed values: action_item, owner, deadline
- Separate multiple values with commas.
- If nothing is missing, return exactly NONE.

request_status
- VALID only if action_item, owner, and deadline are all explicitly provided and valid.
- Otherwise return NEEDS_REVIEW.
- Return only VALID or NEEDS_REVIEW.

Return these fields separately:
- action_item
- owner
- deadline
- meeting_summary
- missing_information
- request_status

Meeting notes:
(here I inserted the Meeting Notes field from the Form)
```

## إيميل Valid (للحضور)
Subject: Meeting Follow-up: (Meeting Title)
```
Hello,

Here is the follow-up from the meeting.

Summary: (Meeting Summary)

Action item: (Action Item)
Owner: (Owner)
Deadline: (Deadline)

Organizer: (Organizer Name)
```

## إيميل المراجعة (للمنظّمة)
Subject: Meeting Follow-up Needs Your Review: (Meeting Title)
```
Hello (Organizer Name),

The follow-up for this meeting could not be sent automatically.

Missing information: (Missing Information)

Original notes:
(Meeting Notes)

Please complete the missing details and send the follow-up manually.
```
