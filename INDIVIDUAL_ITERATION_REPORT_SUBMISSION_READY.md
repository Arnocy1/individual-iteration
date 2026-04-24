# Individual Iteration Report

**Software Engineering Project - Group 11**  
**Individual Iteration Report**  
**Name: Yang Chen**

## 1. User Stories and Features

### Customer Need
For this individual iteration, the customer need was to support **multiple LLM answers for a single user prompt**. Based on that need, I implemented three related features that extend the previous single-LLM chat system from Iteration 2.

### Feature 1: Optional 3-LLM Mode

**User Story**  
As a logged-in user, I want to choose whether three LLMs respond to my question, so that I can decide when I want one answer and when I want multiple answers for comparison.

**Feature Description**  
I added a checkbox labeled **“Have 3 LLMs respond”** on the conversation page. If the checkbox is not selected, the application behaves like the original single-LLM version. If the checkbox is selected, the frontend sends a `useThreeLLM` flag to the backend, and the backend returns three responses instead of one.

**Story Points:** 3

### Feature 2: Display Three Responses and Highlight the Best Answer

**User Story**  
As a logged-in user, I want to see three LLM responses to the same prompt, so that I can compare them and quickly identify the strongest answer.

**Feature Description**  
When 3-LLM mode is enabled, the backend generates three LLM responses and sends them to the frontend. The frontend displays the responses separately and highlights one of them as the best answer. The best answer is selected by an LLM-based judging step in the backend and returned through `bestIndex`.

**Story Points:** 5

### Feature 3: Search and Filter 3-LLM Conversations in History

**User Story**  
As a returning LLM user, I want to search for and filter conversations that used 3-LLM mode, so that I can quickly find conversations where I compared multiple answers.

**Feature Description**  
To support this feature without changing the existing database schema, newly created 3-LLM conversations are saved with a title prefix such as `[3LLM]`. On the history page, users can still search conversations as before, and they can also enable a **3-LLM filter** to display only conversations created in 3-LLM mode.

**Story Points:** 4

### Selection of Features
I selected these three features because together they fully satisfy the customer need. The first feature adds user control, the second feature adds the core multiple-response behavior, and the third feature makes the feature useful over time by integrating it into conversation history. In addition, I made several UI improvements for readability and usability, but those changes were secondary and are not the main focus of this report.

## 2. UI Design

### Number of Pages
The project currently contains five main frontend pages:

1. `index.html` - homepage / landing page  
2. `login.html` - login page  
3. `signup.html` - sign-up page  
4. `conversation.html` - main chat page  
5. `history.html` - conversation history page

### Page Interaction and Transitions
The user interaction flow is as follows:

- A user can register on `signup.html`.
- A returning user can log in on `login.html`.
- After login, the user arrives at `index.html`, which now serves as the landing page.
- Clicking **Start New Chat** opens `conversation.html`, which is the dedicated full chat page.
- On `conversation.html`, the user can type a prompt, click **ASK**, or press **Enter** to send it.
- If the user enables **Have 3 LLMs respond**, the chat page displays three LLM answers and highlights one as the best answer.
- The user can open `history.html` to review previous conversations.
- On `history.html`, the user can search past conversations and optionally filter only the 3-LLM conversations.

### UI Notes for This Iteration
The most important UI changes in this iteration were functional:

- adding the **Have 3 LLMs respond** checkbox,
- displaying three LLM responses in separate sections,
- highlighting the best answer,
- adding a **3-LLM-only** filter to the history page.

Minor visual polishing was also done to improve readability, but the primary goal of the UI work was to support the new 3-LLM workflow.

## 3. Unit Tests and Acceptance Tests

### Deriving Acceptance Tests from Use Cases
As in the previous iterations, acceptance tests are derived from user stories by translating user behavior into **Given / When / Then** scenarios in **Cucumber.js**. The repository already contains Cucumber feature files and Puppeteer step definitions for registration, login, logout, talking to the LLM, saving history, and searching history. For this individual iteration, I added a new acceptance test file for the 3-LLM workflow and a new step definition file for the related browser interactions.

### Acceptance Test 1: Enable 3-LLM Mode

```gherkin
Feature: Optional 3-LLM mode
  As a logged-in user
  I want to choose whether three LLMs respond
  So that I can compare multiple answers only when needed

  Scenario: User enables 3-LLM mode before asking a question
    Given I am logged in as a registered user
    And I am on the conversation page
    When I check "Have 3 LLMs respond"
    And I send the message "Explain recursion"
    Then I should see three LLM responses
```

### Acceptance Test 2: Highlight Best Answer

```gherkin
Feature: Highlight best answer
  As a logged-in user
  I want the best answer to be highlighted
  So that I can quickly identify the strongest response

  Scenario: Best answer is highlighted in 3-LLM mode
    Given I am logged in as a registered user
    And I am on the conversation page
    When I check "Have 3 LLMs respond"
    And I send the message "What is machine learning?"
    Then I should see three LLM responses
    And exactly one response should be highlighted as the best answer
```

### Acceptance Test 3: Filter 3-LLM History

```gherkin
Feature: Filter 3-LLM conversations
  As a returning user
  I want to filter conversations that used 3-LLM mode
  So that I can quickly find comparison-based chats

  Scenario: User filters history to show only 3-LLM conversations
    Given I am logged in as a registered user
    And I have at least one saved 3-LLM conversation
    When I open the history page
    And I enable the 3-LLM filter
    Then I should only see conversations created in 3-LLM mode
```

### Jasmine Unit Test Design
The repository already contains Jasmine tests for reusable authentication helper functions such as email validation, password validation, and normalization logic. For this iteration, I added a dedicated helper file for testable 3-LLM logic and a new Jasmine spec file for it. The new unit tests cover:

- model selection from installed/configured Ollama models,
- fallback best-answer selection,
- judge reply parsing,
- judge model selection.

New files added for this iteration:

- `backend/llmHelpers.js`
- `backend/spec/llmHelpersSpec.js`
- `features/07_threeLLM.feature`
- `features/steps_definitions/threeLLMSteps.js`

### Relationship Between Use Cases and Tests
Each user-facing feature can be broken into smaller behaviors:

- Feature 1 depends on correct checkbox handling and correct transmission of the `useThreeLLM` flag.
- Feature 2 depends on generating three responses, selecting the best response, and highlighting it correctly.
- Feature 3 depends on correctly tagging 3-LLM conversations and filtering them in history.

Acceptance tests validate the full user workflow, while unit tests validate the smaller backend behaviors that support the workflow.

## 4. Software Architecture and Implementation

### REST API Design
The backend is implemented with **Node.js + Express** and uses REST-style routes for authentication, chat, and history.

| Method | Route | Purpose |
|---|---|---|
| `POST` | `/api/auth/signup` | Register a new user |
| `POST` | `/api/auth/login` | Log in and create a session |
| `POST` | `/api/auth/logout` | Log out and destroy the session |
| `GET` | `/api/auth/me` | Check current login state |
| `POST` | `/api/chat` | Send a prompt and receive one reply or three replies |
| `GET` | `/api/history` | Get all conversations for the current user |
| `GET` | `/api/history/search?q=...` | Search conversation history |
| `GET` | `/api/history/:id` | Load one conversation and its messages |

### Routing Table

#### Frontend Pages

| Page | Purpose |
|---|---|
| `index.html` | Landing page |
| `login.html` | User login |
| `signup.html` | User registration |
| `conversation.html` | Full chat page |
| `history.html` | Search and browse saved conversations |

#### Main Backend Logic for 3-LLM Mode
The most important route in this iteration is `POST /api/chat`.

- If `useThreeLLM` is false, the backend behaves like the original single-LLM version.
- If `useThreeLLM` is true, the backend:
  1. resolves three available local Ollama models,
  2. sends the same prompt to all three models,
  3. collects the three responses,
  4. uses an LLM-based judging prompt to select the best answer,
  5. returns the responses and `bestIndex`,
  6. stores the conversation and messages in the database.

### Database Design
The project continues to use the existing SQLite database from the previous iteration with three main tables:

#### `users`
- `id`
- `email`
- `password_hash`

#### `conversations`
- `id`
- `user_id`
- `title`
- `created_at`
- `updated_at`

#### `messages`
- `id`
- `conversation_id`
- `sender`
- `content`
- `created_at`

### 3-LLM Storage Design
One important design decision was to avoid changing the database schema. Instead:

- 3-LLM conversations are identified by a title prefix such as `[3LLM]`,
- the three responses are stored in the existing `messages` table,
- the `sender` field is reused to encode which LLM produced the message, which model was used, and whether the message was selected as best.

This design kept the database compatible with the Iteration 2 structure while still supporting the new multi-LLM feature.

## 5. Instructions

### Installation Instructions

1. Install **Node.js**.
2. Install backend dependencies:

```bash
cd backend
npm install
```

3. Install root-level dependencies if Cucumber is run from the project root:

```bash
npm install
```

4. Install **Ollama** and ensure it is running locally.
5. Pull local models for 3-LLM mode, for example:

```bash
ollama pull qwen2.5:7b
ollama pull llama3.2:3b
ollama pull mistral:7b
```

### Execution Instructions

1. Start the backend:

```bash
cd backend
node server.js
```

2. Start the frontend with Live Server so that the frontend pages are served from:

```text
http://localhost:5500/frontend/
```

3. Open:

```text
http://localhost:5500/frontend/index.html
```

4. Log in or sign up.
5. Click **Start New Chat**.
6. On the chat page, optionally enable **Have 3 LLMs respond**.

**Note:** `localhost` should be used consistently for both frontend and backend. Mixing `localhost` and `127.0.0.1` may cause session and cookie problems.

### Unit Test Instructions

Run Jasmine tests from the backend folder:

```bash
cd backend
npx jasmine
```

At the time of preparing this report, the Jasmine unit tests ran successfully and passed.

### Puppeteer / Cucumber Instructions

Run browser-based acceptance tests:

```bash
npx cucumber-js features
```

Before running these tests:

- the backend should be running on `http://localhost:3000`,
- the frontend should be running on `http://localhost:5500/frontend`,
- the test user credentials used in the feature files should be valid.

For the new 3-LLM acceptance tests, the browser responses are mocked so the scenarios do not depend on live Ollama output. In my development environment, the feature file and step matching were validated successfully. Full browser execution may still depend on local Puppeteer and browser permissions.

## 6. Conclusion

This individual iteration extends the original single-LLM chat application into a comparison-oriented chat system. The most important contribution is the introduction of an **optional 3-LLM mode**, which allows users to request multiple answers to the same prompt, view a highlighted best answer, and later find those conversations through the history page.

Compared with Iteration 2, this work builds on top of the same authentication, history, and SQLite architecture rather than replacing it. The new functionality is integrated through:

- the `useThreeLLM` request flag,
- enhanced backend chat logic,
- LLM-based best-answer selection,
- `[3LLM]` conversation tagging,
- history filtering support.

Overall, this iteration satisfies the customer need of receiving multiple LLM responses for the same question while remaining compatible with the existing project structure.

---

## Suggested Figure Placement and Captions

You can place the figures in the report like this:

### After Section 1 (User Stories and Features)

**Figure 1.** Requirement card for optional **“Have 3 LLMs respond”** checkbox.  
Use: [f791fb515629fb7a5215a78871cd0d42.jpg](C:/Users/Yang/Documents/xwechat_files/wxid_0ap7kyslsn6c12_67fc/temp/RWTemp/2026-04/7a26601344f7711b90441a0187171a2a/f791fb515629fb7a5215a78871cd0d42.jpg)

**Figure 2.** Requirement card for receiving responses from three LLMs and highlighting the best answer.  
Use: [e5c18cb0922453ce4eb4b647bdfc9dea.jpg](C:/Users/Yang/Documents/xwechat_files/wxid_0ap7kyslsn6c12_67fc/temp/RWTemp/2026-04/7a26601344f7711b90441a0187171a2a/e5c18cb0922453ce4eb4b647bdfc9dea.jpg)

**Figure 3.** Requirement card for searching and filtering conversations with 3-LLM responses.  
Use: [10a16f832be71c4b771871475f9db537.jpg](C:/Users/Yang/Documents/xwechat_files/wxid_0ap7kyslsn6c12_67fc/temp/RWTemp/2026-04/7a26601344f7711b90441a0187171a2a/10a16f832be71c4b771871475f9db537.jpg)

### After Section 2 (UI Design)

**Figure 4.** Conversation page with **Have 3 LLMs respond** enabled, showing three responses and a highlighted best answer.  
Recommended screenshot: the chat page after sending a prompt in 3-LLM mode.

**Figure 5.** History page showing the **3-LLM conversation filter**.  
Recommended screenshot: the history page with the checkbox enabled and one or more `[3LLM]` conversations visible.

### Optional Extra Figure

**Figure 6.** Landing page after login, showing the entry point to the new chat workflow.  
This figure is optional. Only include it if you want to show the updated page transition from homepage to the dedicated chat page.
