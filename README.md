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

**Setup Instructions:**

1.  **Replace with Your Email ID:**
    Update the `EMAIL` constant in the code with the email address associated with your Jira account:

    ```python
    EMAIL = "your-email@example.com"  # Replace with your own email
    ```

2.  **Generate Jira API Token:**
    Generate an API token for Jira authentication by visiting the following link:
    [Generate API Token]([invalid URL removed])
    Replace the `API_TOKEN` constant in the code with the generated token:

    ```python
    API_TOKEN = "your-generated-api-token"  # Replace with your generated API token
    ```

3.  **Set Up Your Jira Base URL:**
    Create a project on Jira and navigate to your Jira board. Identify your Jira domain (workspace) and update the `JIRA_BASE_URL` constant. The base URL follows the format `https://your-domain.atlassian.net`. Replace `your-domain` with your actual Jira workspace name.

    For example, if your workspace name is `exampleworkspace`, it should be:

    ```python
    JIRA_BASE_URL = "[https://exampleworkspace.atlassian.net](https://exampleworkspace.atlassian.net)"
    ```

4.  **Verify and Update Your Project Key:**
    On your Jira board, create a test ticket to verify your project key. The project key is the prefix found in your issue keys (e.g., CPG, FTS, FT). Update the `PROJECT_KEY` constant in the code accordingly:

    ```python
    PROJECT_KEY = "YOUR_PROJECT_KEY"  # Replace with your actual project key (e.g., CPG, FTS, FT, etc.)
    ```

5.  **(Optional) Update Gemini API Keys:**
    The code includes default Gemini API keys in the `gemini_keys` list. These should function as provided. However, if you wish to generate new keys, you can do so by visiting:
    [Generate Gemini API Key]([invalid URL removed])
    If you generate your own API keys, update the `gemini_keys` list in the code:

    ```python
    gemini_keys = ['your-first-gemini-api-key', 'your-second-gemini-api-key']
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

