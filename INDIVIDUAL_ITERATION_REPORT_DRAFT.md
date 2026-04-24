# Individual Iteration Report Draft

**Course Project Individual Iteration Report - Yang Chen**

This draft follows the general structure of the Iteration 2 report, but focuses on the individual feature set added in this iteration. The main goal of this iteration was to support **multiple LLM responses in one conversation flow**, with optional 3-LLM mode, best-answer highlighting, and history support for 3-LLM conversations. Minor UI improvements were also made, but they are treated as supporting changes rather than the main contribution of this report.

## 1. User Stories and Features

### 1.1 Customer Need
The customer need for this individual iteration was: **“I would like to get answers from multiple LLMs simultaneously.”**

Based on that need, I selected three closely related features that extend the original single-LLM chat system from Iteration 2.

### 1.2 User Story 1: Optional 3-LLM Response Mode

**Story**

As a logged-in user,  
I want to choose whether three LLMs respond to my question or not,  
so that I can decide when I want a single answer and when I want multiple answers for comparison.

**Feature**

I added a checkbox labeled **“Have 3 LLMs respond”** on the conversation page. When the checkbox is not selected, the system behaves like the original single-LLM version. When it is selected, the frontend sends a `useThreeLLM` flag to the backend, and the backend returns three answers instead of one.

**Story Points:** 3

### 1.3 User Story 2: Display Three LLM Responses and Highlight the Best Answer

**Story**

As a logged-in user,  
I want to see answers from three LLMs for the same prompt,  
so that I can compare them and quickly notice which answer is the strongest one.

**Feature**

When 3-LLM mode is enabled, the backend generates three LLM responses and returns them to the frontend. The interface displays the three responses separately and highlights one of them as the **best answer**. In the current implementation, the backend uses an LLM-based judge prompt to choose the best response and returns `bestIndex` to the frontend. The highlighted response is shown with a special visual style so that it is easy to identify.

**Story Points:** 5

### 1.4 User Story 3: Search and Filter 3-LLM Conversations in History

**Story**

As a returning LLM user,  
I want to search for and filter conversations that used 3-LLM mode,  
so that I can quickly find conversations where I previously compared multiple responses.

**Feature**

To support this feature without changing the database schema, newly created 3-LLM conversations are saved with a title prefix such as `[3LLM] ...`. On the history page, users can search conversation history as before and can also enable a **3-LLM-only filter** to focus on comparison conversations. This keeps the history system compatible with the existing Iteration 2 architecture while still making multi-LLM conversations easy to identify.

**Story Points:** 4

### 1.5 Selection of Features

I selected all three features above because together they fully implement the customer need instead of only partially addressing it.

- User Story 1 gives the user control over whether multi-LLM mode is enabled.
- User Story 2 delivers the core comparison behavior by producing three answers and highlighting the best one.
- User Story 3 makes the feature useful over time by integrating 3-LLM conversations into history and search.

The additional UI polishing done during this iteration improved readability and usability, but those changes were secondary to the three main 3-LLM features listed above.

## 2. UI Design

### 2.1 Pages in the Project

The project currently contains five main frontend pages:

1. `index.html` - landing page / homepage  
2. `login.html` - login page  
3. `signup.html` - registration page  
4. `conversation.html` - full chat workspace  
5. `history.html` - conversation history page  

### 2.2 User Interaction and Page Transitions

The interaction flow is now more clearly separated than in the previous version:

- A new user can sign up on `signup.html`.
- A returning user can log in on `login.html`.
- After login, the user arrives at `index.html`, which now acts as a landing page instead of a full chat page.
- Clicking **Start New Chat** opens `conversation.html`, which is the dedicated page for sending prompts and viewing responses.
- On `conversation.html`, the user can:
  - enter a prompt,
  - press **ASK** or hit **Enter**,
  - optionally enable **Have 3 LLMs respond**.
- If 3-LLM mode is enabled, the page shows three responses and highlights the best one.
- From `conversation.html`, the user can open `history.html` to browse old conversations.
- On `history.html`, the user can:
  - search old conversations,
  - open a saved conversation,
  - filter only 3-LLM conversations,
  - start a new chat.

### 2.3 UI Notes for This Iteration

The major UI change in this iteration was functional rather than decorative:

- the conversation page now includes the **Have 3 LLMs respond** checkbox,
- the three responses are displayed as separate cards,
- the best response is visually highlighted,
- the history page includes a **Show only 3-LLM conversations** filter,
- the homepage was simplified so that it works as an entry page rather than trying to act like a full chat workspace.

For the report, I would attach:

- the handwritten requirement cards from class,
- one screenshot of the conversation page with 3-LLM mode enabled,
- one screenshot of the history page showing the 3-LLM filter.

## 3. Unit Tests and Acceptance Tests

### 3.1 Deriving Acceptance Tests from Use Cases and Scenarios

As in the previous iterations, acceptance tests are derived from user stories by translating user behavior into **Gherkin scenarios** using **Cucumber.js**. The existing repository already contains Cucumber feature files for registration, login, logout, talking to the LLM, saving history, and searching history. For this individual iteration, I derived new acceptance scenarios that target the 3-LLM functionality.

The derivation process is:

1. Start from a user story.
2. Identify the user goal and the visible system behavior.
3. Translate that behavior into Given/When/Then form.
4. Automate browser interaction with Puppeteer where appropriate.

### 3.2 Acceptance Test Scenarios for This Iteration

#### Acceptance Test 1: Enable 3-LLM Mode

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

#### Acceptance Test 2: Best Answer Highlight

```gherkin
Feature: Highlight best answer
  As a logged-in user
  I want the best answer to be highlighted
  So that I can quickly identify the strongest response

  Scenario: Best answer is visually highlighted in 3-LLM mode
    Given I am logged in as a registered user
    And I am on the conversation page
    When I check "Have 3 LLMs respond"
    And I send the message "What is machine learning?"
    Then I should see three LLM responses
    And exactly one response should be highlighted as the best answer
```

#### Acceptance Test 3: Filter 3-LLM History

```gherkin
Feature: Filter 3-LLM conversations in history
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

### 3.3 Jasmine Test Design

The repository already includes Jasmine unit tests for reusable authentication helper functions such as:

- email validation,
- password validation,
- login input normalization,
- database existence checks.

For this individual iteration, the same unit-testing style can be extended to the new 3-LLM helper logic in the backend. The most testable units are:

- `getFallbackBestIndex(replies)`
- `resolveThreeLLMModels()`
- `pickBestReplyWithLLM(userPrompt, replies, judgeModel)`

Example Jasmine test design:

```js
describe("getFallbackBestIndex", () => {
    it("returns the index of the longest reply", () => {
        const replies = ["short", "this is the longest reply", "medium"];
        expect(getFallbackBestIndex(replies)).toBe(1);
    });
});
```

```js
describe("resolveThreeLLMModels", () => {
    it("returns three distinct installed models when available", async () => {
        // mock available model list from Ollama
        // expect three unique model names
    });
});
```

```js
describe("pickBestReplyWithLLM", () => {
    it("falls back to the longest reply if the judge output is invalid", async () => {
        // mock judge output
        // expect fallback index
    });
});
```

### 3.4 Relationship Between Unit Tests and Use Cases

The use cases are implemented through smaller behaviors that can be validated separately.

- User Story 1 depends on correct checkbox handling and transmission of `useThreeLLM`.
- User Story 2 depends on generating three responses, choosing a best response, and rendering the highlight correctly.
- User Story 3 depends on correctly marking 3-LLM conversations and filtering history results.

Therefore, acceptance tests validate the user-facing workflow, while unit tests validate the smaller backend decisions that make the workflow possible.

## 4. Software Architecture and Implementation

### 4.1 REST API Design

The backend is implemented using **Node.js + Express** and exposes REST-style endpoints for authentication, chat, and history.

#### Main REST Endpoints

| Method | Route | Purpose |
|---|---|---|
| `POST` | `/api/auth/signup` | Register a new user |
| `POST` | `/api/auth/login` | Log in and create a session |
| `POST` | `/api/auth/logout` | Log out and destroy the session |
| `GET` | `/api/auth/me` | Check current login state |
| `POST` | `/api/chat` | Send a prompt and receive either one reply or three replies |
| `GET` | `/api/history` | Get all conversations for the current user |
| `GET` | `/api/history/search?q=...` | Search conversation history |
| `GET` | `/api/history/:id` | Load one conversation and all of its messages |

### 4.2 Routing Table

#### Frontend Page Routing

| Page | Purpose | Main Navigation |
|---|---|---|
| `index.html` | Landing page | Start New Chat, Login, Signup, History |
| `login.html` | Log in | Home, Sign Up |
| `signup.html` | Create account | Home, Login |
| `conversation.html` | Main chat workspace | Back to History, Logout |
| `history.html` | Search and browse conversations | Home, New Chat, Logout |

#### Backend Routing Logic for 3-LLM Mode

The most important route added for this iteration is `POST /api/chat`.

- If `useThreeLLM` is `false`, the system behaves like the original single-LLM design.
- If `useThreeLLM` is `true`, the backend:
  1. resolves up to three available Ollama models,
  2. sends the same user prompt to the three models,
  3. collects the three replies,
  4. uses a judge prompt to select the best reply,
  5. returns the replies plus `bestIndex`,
  6. stores the conversation and messages in SQLite.

### 4.3 Database Design

The project continues to use the existing SQLite database with three main tables:

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

### 4.4 3-LLM Storage Strategy

An important design decision in this iteration was to **avoid adding new database columns**. Instead:

- 3-LLM conversations are marked using a title prefix such as `[3LLM]`.
- Individual multi-LLM replies are stored in the existing `messages` table.
- The `sender` field encodes extra information such as:
  - which LLM produced the response,
  - which model was used,
  - whether the response was selected as best.

This approach allowed the new feature to be implemented while preserving compatibility with the Iteration 2 database schema and history routes.

## 5. Instructions for Reproducing the Results

### 5.1 Installation Instructions

1. Install **Node.js**.
2. Install backend dependencies:

```bash
cd backend
npm install
```

3. Install root-level dependencies if acceptance tests are executed from the repository root:

```bash
npm install
```

4. Install **Ollama** and make sure it is running locally.
5. Pull the local LLM models. For full 3-LLM mode, at least three models should be installed. Example:

```bash
ollama pull qwen2.5:7b
ollama pull llama3.2:3b
ollama pull mistral:7b
```

### 5.2 Execution Instructions

1. Start the backend:

```bash
cd backend
node server.js
```

2. Start the frontend with Live Server so that pages are served from:

```text
http://localhost:5500/frontend/
```

3. Open:

```text
http://localhost:5500/frontend/index.html
```

4. Log in or sign up.
5. Click **Start New Chat**.
6. Enable **Have 3 LLMs respond** if you want multiple responses.

**Note:** `localhost` should be used consistently for both frontend and backend. Mixing `localhost` and `127.0.0.1` may break session/cookie behavior.

### 5.3 Unit Test Instructions

Run Jasmine tests from the backend folder:

```bash
cd backend
npx jasmine
```

These tests currently validate authentication-related helper logic and demonstrate the unit-testing structure used by the project.

### 5.4 Puppeteer / Cucumber Instructions

To run automated browser-based acceptance tests:

```bash
npx cucumber-js features
```

Before running acceptance tests:

- make sure the backend is running at `http://localhost:3000`,
- make sure the frontend is running at `http://localhost:5500/frontend`,
- make sure the test user credentials used in the feature steps are valid.

## 6. Conclusion

This individual iteration extends the original single-LLM chat application into a comparison-oriented chat system. The most important contribution is the introduction of an **optional 3-LLM mode**, which lets users request multiple answers for the same prompt, see a highlighted best answer, and later search or filter those multi-LLM conversations in history.

Compared with Iteration 2, this iteration does not replace the existing architecture; instead, it builds on top of it. The same authentication flow, history flow, and SQLite schema are reused, while the 3-LLM capability is integrated through:

- a new `useThreeLLM` request flag,
- enhanced backend chat logic,
- a best-answer selection step,
- title-based tagging of 3-LLM conversations,
- history filtering support.

Overall, this iteration satisfies the customer need of receiving answers from multiple LLMs simultaneously while staying compatible with the earlier project structure.
