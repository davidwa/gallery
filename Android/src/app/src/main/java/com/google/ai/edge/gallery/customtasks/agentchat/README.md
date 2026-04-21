# Skill Result Injection

The Agent Chat task supports injecting the results of JavaScript skills directly into the system prompt. This allows the model to have access to dynamic data (like current weather, calendar events, or device status) at the start of a session.

## Usage

To inject a skill result, add a placeholder to the system prompt in the following format:

`___SKILL_RESULT_skillName:scriptName:inputData___`

- **skillName**: The name of the skill as defined in the Skill Manager.
- **scriptName**: The name of the specific script within that skill to execute.
- **inputData**: The input string to pass to the script's `main` function.

### Example
`Current weather info: ___SKILL_RESULT_Weather:getForecast:Berlin___`

## How it Works
1. When a session is initialized (or reset), the system prompt is scanned for these placeholders.
2. For each placeholder, the corresponding JavaScript skill is executed in the background WebView.
3. The skill is expected to return a JSON object with a `result` field: `{"result": "..."}`.
4. The placeholder is replaced by the value of the `result` field.
5. If the skill fails or the result is not in the expected format, the placeholder remains or is replaced by an error message.

## Implementation Details
- **Regex Parsing**: Handled in `SkillManagerViewModel.kt`.
- **JSON Extraction**: The `runSkillScript` helper in `AgentTools.kt` uses Moshi to extract only the `result` field to avoid leaking raw JSON into the prompt.
- **Sequential Execution**: Results are fetched sequentially during the prompt preparation phase.
