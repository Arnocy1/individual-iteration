# Report Finishing Guide

This note is a short companion to `INDIVIDUAL_ITERATION_REPORT_SUBMISSION_READY.md`.  
It is meant to help convert the draft into a more natural final submission in Word.

## 1. Tone Adjustments

The current report is already submission-ready, but if you want it to sound more like a student report and less formal, keep these edits in mind:

### Good style to keep

- Use **I** instead of **we**, because this is an individual iteration.
- Keep the focus on the three main 3-LLM features.
- Mention UI improvements only briefly.
- Be honest about the test status: existing test framework is present, and new 3-LLM test scenarios were designed based on that framework.

### Sentences you can keep as-is

- “For this individual iteration, the customer need was to support multiple LLM answers for a single user prompt.”
- “I selected these three features because together they fully satisfy the customer need.”
- “The project continues to use the existing SQLite database from the previous iteration.”

### Phrases to avoid overusing

These phrases are not wrong, but if repeated too much they sound too polished:

- “fully satisfy the customer need”
- “comparison-oriented chat system”
- “remaining compatible with the existing project structure”

If you want, replace one or two of them with simpler wording.

### More natural alternatives

Instead of:

> “The most important contribution is the introduction of an optional 3-LLM mode.”

You can write:

> “The main feature I added in this iteration is an optional 3-LLM mode.”

Instead of:

> “This work builds on top of the same authentication, history, and SQLite architecture rather than replacing it.”

You can write:

> “This iteration keeps the same login, history, and SQLite design from the previous version and extends it with the new 3-LLM feature.”

Instead of:

> “The new functionality is integrated through...”

You can write:

> “I implemented the new functionality through...”

## 2. What Not to Overclaim

Keep these points accurate when you submit:

- The repository already has **Jasmine**, **Cucumber**, and **Puppeteer** infrastructure.
- The current automated tests in the repository are mainly for authentication and Iteration 2 history/chat flows.
- For this individual iteration, you can say that you **designed new acceptance-test scenarios and unit-test plans** for the 3-LLM features using the same testing approach.
- Do **not** say that all 3-LLM test automation has already been fully implemented unless you actually add those test files.

## 3. Screenshot Checklist

For this report, the most useful screenshots are not the decorative UI ones.  
Use screenshots that directly support the three individual features.

### Screenshot 1: Requirement Card - 3 LLM Checkbox

Use:

- `f791fb515629fb7a5215a78871cd0d42.jpg`

Caption:

> Figure 1. Requirement card for adding the “Have 3 LLMs respond” checkbox.

### Screenshot 2: Requirement Card - Best Answer from 3 LLMs

Use:

- `e5c18cb0922453ce4eb4b647bdfc9dea.jpg`

Caption:

> Figure 2. Requirement card for displaying responses from three LLMs and highlighting the best answer.

### Screenshot 3: Requirement Card - Search / Filter 3-LLM Conversations

Use:

- `10a16f832be71c4b771871475f9db537.jpg`

Caption:

> Figure 3. Requirement card for searching and filtering conversations that used 3-LLM mode.

### Screenshot 4: Conversation Page with 3-LLM Responses

Take a screenshot of:

- `conversation.html`
- checkbox enabled
- one prompt sent
- three responses visible
- one response highlighted as best

Caption:

> Figure 4. Conversation page with 3-LLM mode enabled, showing three responses and the highlighted best answer.

### Screenshot 5: History Page with 3-LLM Filter

Take a screenshot of:

- `history.html`
- `Show only 3-LLM conversations` enabled
- at least one 3-LLM conversation visible in the list

Caption:

> Figure 5. History page showing the filter for 3-LLM conversations.

### Optional Screenshot 6: Homepage / Landing Page

Only include this if you still have space and want to show navigation flow.

Take a screenshot of:

- `index.html`
- login state visible
- `Start New Chat` and `View History` buttons visible

Caption:

> Figure 6. Landing page after login, which serves as the entry point for starting a new chat or opening conversation history.

## 4. Best Order for Figures in the Report

Recommended placement:

1. Put **Figures 1–3** right after **Section 1: User Stories and Features**
2. Put **Figure 4** in **Section 2: UI Design**
3. Put **Figure 5** at the end of **Section 2** or start of **Section 4**
4. Put **Figure 6** only if you want one extra UI/navigation screenshot

## 5. Final Submission Tips

Before submitting:

- Make sure the report uses **Yang Chen** only as the author for the individual iteration.
- Keep the requirement cards because they directly satisfy the “use cases and Lo-Fi UI sketches” requirement.
- If your report starts getting too long, shorten the UI discussion first, not the 3-LLM features.
- Keep the routing table and database design section, because those are explicitly required.
- If needed, you can shorten the test section by keeping one paragraph of explanation plus the three Gherkin scenarios.

## 6. If You Need to Cut Length Quickly

Cut in this order:

1. Shorten the UI notes
2. Shorten the repeated explanation in the conclusion
3. Shorten the installation instructions

Do **not** cut:

- the three user stories,
- the routing table,
- the database design,
- the three acceptance test scenarios.
