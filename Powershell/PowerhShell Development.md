# PowerhShell Development

Setting up Visual Studio Code (VS Code) for PowerShell development. Here's a breakdown of what you're doing:

1. **Listing and Installing Extensions in VS Code**:
   - `code --list-extensions`: This command, when run in the terminal, lists all the installed extensions in your VS Code setup.
   - `code --install-extension ms-vscode.PowerShell`: This command installs the PowerShell extension for VS Code, which is essential for PowerShell scripting within the editor.

2. **PowerShell Base VS Code Settings**:
   Your JSON configuration sets up various settings for the PowerShell environment in VS Code. Let's go through the key settings:

   - `"editor.quickSuggestionsDelay": 1`: Sets a delay of 1 millisecond for displaying quick suggestions.
   - `"editor.tabCompletion": "on"`: Enables tab completion in the editor, which can help speed up coding.
   - `"files.defaultLanguage": "powershell"`: Sets PowerShell as the default language for new files.

   - PowerShell Specific Settings:
     - `"powershell.powerShellAdditionalExePaths"`: Specifies custom paths for different versions of PowerShell. Useful if you have multiple versions installed and want to switch between them easily in VS Code.
     - `"powershell.powerShellDefaultVersion": "PS7"`: Sets PowerShell 7 as the default version to use in the integrated console.
     - `"powershell.codeFormatting.preset":"Stroustrup"`: Applies the Stroustrup code formatting style to your PowerShell scripts.
     - `"powershell.startAutomatically": true`: Ensures that the PowerShell extension starts automatically when you open a PowerShell file.
     - `"powershell.scriptAnalysis.enable": true`: Enables script analysis, which can provide code suggestions and best practices.
     - `"powershell.integratedConsole.showOnStartup": false`: Prevents the integrated console from showing up automatically on startup.
     - `"powershell.integratedConsole.focusConsoleOnExecute": true`: Focuses on the console when you execute a script, which is useful for seeing output or entering data.

These settings optimize your VS Code environment for an efficient PowerShell scripting experience, tailored to personal preferences and workflow. It's a good setup for both learning and professional script development.