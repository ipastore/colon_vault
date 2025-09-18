## [Keyboard shortcuts for macOS](https://docs.github.com/en/copilot/reference/keyboard-shortcuts#keyboard-shortcuts-for-macos)

| Action                                                        | Shortcut                 |
| :------------------------------------------------------------ | :----------------------- |
| Accept an inline suggestion                                   | Tab                      |
| Dismiss an inline suggestion                                  | Esc                      |
| Show next inline suggestion                                   | Option (⌥) or Alt+]      |
| Show previous inline suggestion                               | Option (⌥) or Alt+[      |
| Trigger inline suggestion                                     | Option (⌥)+\|            |
| Open GitHub Copilot (additional suggestions in separate pane) | Option (⌥) or Alt+Return |
## [Slash commands](https://docs.github.com/en/copilot/reference/cheat-sheet#slash-commands-1)

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by the command name.

Available slash commands may vary, depending on your environment and the context of your chat. To view a list of currently available slash commands, type `/` in the chat prompt box of your current environment. Below is a list of some of the most common slash commands for using Copilot Chat.

|Command|Description|
|---|---|
|`/clear`|Start a new chat session.|
|`/explain`|Explain how the code in your active editor works.|
|`/fix`|Propose a fix for problems in the selected code.|
|`/fixTestFailure`|Find and fix a failing test.|
|`/help`|Quick reference and basics of using GitHub Copilot.|
|`/new`|Create a new project.|
|`/tests`|Generate unit tests for the selected code.|
## [Chat variables](https://docs.github.com/en/copilot/reference/cheat-sheet#chat-variables)

Use chat variables to include specific context in your prompt. To use a chat variable, type `#` in the chat prompt box, followed by a chat variable.

| Variable     | Description                                            |
| ------------ | ------------------------------------------------------ |
| `#block`     | Includes the current block of code in the prompt.      |
| `#class`     | Includes the current class in the prompt.              |
| `#comment`   | Includes the current comment in the prompt.            |
| `#file`      | Includes the current file's content in the prompt.     |
| `#function`  | Includes the current function or method in the prompt. |
| `#line`      | Includes the current line of code in the prompt.       |
| `#path`      | Includes the file path in the prompt.                  |
| `#project`   | Includes the project context in the prompt.            |
| `#selection` | Includes the currently selected text in the prompt.    |
| `#sym`       | Includes the current symbol in the prompt.             |

## [Chat participants](https://docs.github.com/en/copilot/reference/cheat-sheet#chat-participants)

Chat participants are like domain experts who have a specialty that they can help you with. You can specify a chat participant by typing `@` in the chat prompt box, followed by a chat participant name. To see all available chat participants, type `@` in the chat prompt box.

Below is a list of some of the most common chat participants for using Copilot Chat.

|Variable|Description|
|---|---|
|`@azure`|Has context about Azure services and how to use, deploy and manage them. Use `@azure` when you want help with Azure. The `@azure` chat participant is currently in public preview and is subject to change.|
|`@github`|Allows you to use GitHub-specific Copilot skills. See [Asking GitHub Copilot questions in your IDE](https://docs.github.com/en/copilot/using-github-copilot/asking-github-copilot-questions-in-your-ide#using-github-skills-for-copilot).|
|`@terminal`|Has context about the Visual Studio Code terminal shell and its contents. Use `@terminal` when you want help creating or debugging terminal commands.|
|`@vscode`|Has context about Visual Studio Code commands and features. Use `@vscode` when you want help with Visual Studio Code.|
|`@workspace`|Has context about the code in your workspace. Use `@workspace` when you want Copilot to consider the structure of your project, how different parts of your code interact, or design patterns in your project.|
### Stackoverflow
@askstackoverflow: Question

### Mermaid Chart
- **[@Mermaid-Chart](https://github.com/Mermaid-Chart)** What diagrams do you support?
- **In VS Code, add [chat context](https://code.visualstudio.com/docs/copilot/getting-started-chat#_add-chat-context) to supercharge your queries**
    - **[@Mermaid-Chart](https://github.com/Mermaid-Chart)** Can you make a flowchart for my GitHub Action **#file**?
    - **[@Mermaid-Chart](https://github.com/Mermaid-Chart)** Make an class diagram for the class in the **#selection**.
    - **[@Mermaid-Chart](https://github.com/Mermaid-Chart)** Visualize this database schema from an SQL **#file**.
    - (using **Attach Context -> Current Editor**) **[@Mermaid-Chart](https://github.com/Mermaid-Chart)** Can you make me a mindmap of the current open file?



