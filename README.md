# Project: Automated Task and Test Case Generation with Gemini API and Jira Integration

## Overview
This project demonstrates an end-to-end automation pipeline implemented in a single Python script, `flight_tracking_application_honeywell.py`. A manager assigns a high-level development requirement, and two AI-driven agents (powered by the Gemini API) handle:

1. **Requirement Agent**: Analyzes the requirement, generates 3–5 high-level development subtasks, and creates corresponding Jira tickets.
2. **Test Agent**: For each development subtask, generates 3–5 comprehensive test cases and adds them as Jira subtasks.

Automating subtask and test case creation accelerates sprint planning, ensures consistency, and reduces manual overhead.

## File Structure

- **`flight_tracking_application_honeywell.py`**: Contains all functionality:
  - Manager input handling
  - Requirement agent prompt construction and AI calls
  - Test agent prompt construction and AI calls
  - JSON parsing of AI responses
  - Jira API integration for creating subtasks and test-case subtasks
  - Utility functions for logging and error handling

## Code Overview

### Manager Flow
- The manager provides a requirement and a unique `task_id`.
- The script invokes the requirement agent to generate subtasks.

### Requirement Agent
1. **Prompt Construction**: Builds an instruction template with the requirement text.
2. **AI Call**: Sends the prompt to the Gemini API via `request_ai_completion()`.
3. **Response Parsing**: Extracts a JSON array of subtasks from the AI response.
4. **Jira Subtask Creation**: Calls `add_subtask()` to create each subtask in Jira.

### Test Agent
1. **Prompt Construction**: Builds a test-case-generation instruction for each subtask.
2. **AI Call**: Sends the test prompt to Gemini API.
3. **Response Parsing**: Extracts JSON array of test cases.
4. **Jira Subtask Creation**: Adds each test case as a subtask under its parent development subtask.

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
3. **Configure environment variables**:
   ```dotenv
   GEMINI_API_KEY=your_gemini_api_key
   JIRA_BASE_URL=https://your-domain.atlassian.net
   JIRA_USER_EMAIL=you@example.com
   JIRA_API_TOKEN=your_jira_token
   ```

## Usage
1. **Assign a Requirement**:
   ```bash
   python flight_tracking_application_honeywell.py --task "Implement user authentication flow"
   ```
2. **Review Created Subtasks** in Jira.
3. **Run Test Agent** (automatically triggered after subtasks creation or manually):
   ```bash
   python flight_tracking_application_honeywell.py --parent-task TASK-1234 --run-tests
   ```
4. **Verify Test Case Subtasks** in Jira.

## Error Handling
- **JSON Parsing Errors**: Logged with raw AI output for debugging.
- **Jira API Failures**: Retries up to 3 times, then logs and skips.
- **Gemini API Errors**: Logged with status codes and messages.

## Extensibility
- **Additional Agents**: Extend the script with more AI agents for documentation, code review, or deployment.
- **Custom Prompts**: Modify the prompt templates in the script for different task structures.

## License
This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

