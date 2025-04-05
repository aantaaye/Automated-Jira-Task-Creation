# Project: Automated Task and Test Case Generation with Gemini API and Jira Integration

## Overview
This project demonstrates an end-to-end automation pipeline where a manager assigns a high-level development requirement, and two AI-driven agents (powered by Gemini API) handle the following:

1. **Requirement Agent**: Analyzes the incoming requirement and generates 3–5 high-level development subtasks. Each subtask is then created as a Jira ticket via the Jira API.
2. **Test Agent**: For each development subtask, this agent generates 3–5 comprehensive test cases, each covering critical functionality or edge cases. The test cases are then added as subtasks to the corresponding Jira ticket.

By automating subtask and test case creation, the system accelerates sprint planning, ensures consistency, and reduces manual overhead.

## Architecture

```plaintext
+----------------+       +----------------+       +--------------+       +----------------+
| Manager Client |  -->  | Requirement    |  -->  | Jira API     |  -->  | Jira Tickets   |
| (Assigns Task) |       | Agent (Gemini) |       | Integration  |       | (Subtasks)     |
+----------------+       +----------------+                             +----------------+
                                           |
                                           v
                                   +----------------+
                                   | Test Agent     |
                                   | (Gemini)       |
                                   +----------------+
                                           |
                                           v
                                   +----------------+
                                   | Jira API       |
                                   | (Test Cases)   |
                                   +----------------+
```  

## Key Components

- **`manager.py`**: Entry point where the manager submits a high-level requirement. It invokes the Requirement Agent.
- **`requirement_agent.py`**: Contains the function `create_subtasks(requirement_text, task_id)`, which:
  1. Constructs a prompt to generate 3–5 subtasks.
  2. Calls `request_ai_completion()` using Gemini API.
  3. Parses the JSON response and creates Jira subtasks via `add_subtask()`.
- **`test_agent.py`**: Defines `create_test_cases(task_details, task_id)`, which:
  1. Builds a prompt to generate 3–5 test cases per subtask.
  2. Sends the prompt to Gemini API.
  3. Parses the JSON and creates Jira subtasks for test cases.
- **`jira_integration.py`**: Utility module that handles authentication, HTTP requests, and payload formatting for Jira’s REST API.
- **`utils.py`**: Shared helper functions such as JSON parsing, logging, and error handling.

## Code Overview

### 1. Manager Submission
- The manager provides a requirement and a unique `task_id`.
- `manager.py` calls `create_subtasks(requirement_text, task_id)` from `requirement_agent.py`.

### 2. Requirement Agent Flow (`requirement_agent.py`)
1. **Prompt Construction**: A detailed instruction template is filled with the requirement text.
2. **AI Call**: `request_ai_completion(prompt)` sends the instruction to Gemini API.
3. **Response Parsing**: The raw text response is scanned for a JSON array of subtasks.
4. **Jira Subtask Creation**: For each subtask, `add_subtask(task_id, title, description)` invokes the Jira API and records the returned ticket key.
5. **Return**: A list of created subtask keys is returned to the manager script.

### 3. Test Agent Flow (`test_agent.py`)
1. **Prompt Construction**: Similar to the requirement agent, but focused on test cases.
2. **AI Call**: Sends the test-case-generation prompt to Gemini.
3. **Response Parsing**: Extracts JSON array of test cases.
4. **Jira Subtask Creation**: Each test case is added as a subtask under its parent development subtask.
5. **Return**: The created test-case subtask keys.

## Dependencies
- Python 3.9+
- `requests` for HTTP calls
- `json` for parsing AI responses
- Gemini API client (internal)
- Jira REST API credentials (URL, API token, user email)

## Setup
1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-org/auto-task-generator.git
   cd auto-task-generator
   ```
2. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```
3. **Configure environment variables** (e.g., in a `.env` file):
   ```dotenv
   GEMINI_API_KEY=your_gemini_api_key
   JIRA_BASE_URL=https://your-domain.atlassian.net
   JIRA_USER_EMAIL=you@example.com
   JIRA_API_TOKEN=your_jira_token
   ```

## Usage
1. **Assign a Requirement**:
   ```bash
   python manager.py --task "Implement user authentication flow"
   ```
2. **Review Created Subtasks** in Jira.
3. **Test Agent Execution** (can be triggered automatically after subtasks creation or manually):
   ```bash
   python test_agent.py --parent-task TASK-1234
   ```
4. **Verify Test Case Subtasks** in Jira.

## Error Handling
- **JSON Parsing Errors**: Logged with raw AI output for debugging.
- **Jira API Failures**: Retries up to 3 times, then logs and skips.
- **Gemini API Errors**: Logged with status codes and messages.

## Extensibility
- **Additional Agents**: Add more AI agents for documentation, code review, or deployment tasks.
- **Custom Prompts**: Modify prompt templates in `requirement_agent.py` and `test_agent.py` for different task structures.

## License
This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

