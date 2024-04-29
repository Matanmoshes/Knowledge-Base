<#
we want to be notified when a drive in our home network is running low on space.
The script will check the desired drive for free space, 
and send a Telegram notification if it is below 20%.
All actions should be logged.
The script should support scannig a drive the user specifies.
The script should support both Linux and Windows.
#>
<#
Supports loggigng 
Checks desired drive for free space
Lets user specify drive 
Supports both windows and linux
send Telegram notification
#>


The script you've provided is quite comprehensive and well-structured, serving the purpose of monitoring drive space and sending notifications via Telegram if the space falls below a certain threshold. Here's a breakdown and analysis to help you understand its components and functionalities:


### Script Breakdown

First, ensure you have the `PoshGram` module installed. You can install it from the PowerShell Gallery with the following command:

```powershell
Install-Module -Name PoshGram -Scope CurrentUser
```
#### **1. Execution Policies and Script Handling**
This part of your script handles the execution policies in PowerShell, which determine how PowerShell loads configuration files and runs scripts. This ensures the script can execute without security restrictions that might block it.

```powershell
# List current execution policies
Get-ExecutionPolicy -List

# Set execution policy to allow local scripts and downloaded scripts that are signed
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# Unblock a script downloaded from the internet
Unblock-File -Path .\drive_warn.ps1

# Run a local script
.\drive_warn.ps1

# Run a local script in the current scope
. .\drive_warn.ps1
```

#### **2. Script Example with Parameters**
The main part of your script includes parameters and logic for determining drive space, logging actions, and sending notifications if necessary.

- **Parameters**: The script starts by defining a mandatory parameter `$Drive`, which specifies which drive to monitor.
  
- **Platform-Specific Paths**: It sets log paths depending on the operating system, accommodating both Windows and Linux platforms.

- **Logging Setup**: It checks if the log directory and file exist; if not, it creates them. It logs every significant step to help with debugging and tracking the script's operations.

- **Drive Space Checking**: The script checks the specified drive's free space percentage. It handles both Unix-like and Windows systems differently due to differences in cmdlets and object properties available on these platforms.

- **Telegram Notification**: If the free space percentage is below the set threshold, it imports the `PoshGram` module, constructs a message, and sends it using the Telegram bot.

```powershell
param (
    [Parameter(Mandatory = $true)]
    [string] $Drive
)

# Different log path based on OS
if ($PSVersionTable.Platform -eq 'Unix') {
    $logPath = '/tmp'
} else {
    $logPath = 'C:\Logs'
}

$logFile = "$logPath\driveCheck.log"

# Ensure logging directory and file exist
try {
    if (-not (Test-Path -Path $logPath -ErrorAction Stop)) {
        New-Item -ItemType Directory -Path $logPath -ErrorAction Stop | Out-Null
        New-Item -ItemType File -Path $logFile -ErrorAction Stop | Out-Null
    }
} catch {
    throw
}

Add-Content -Path $logFile -Value "[INFO] Running $PSCommandPath"

# Checking drive space and logging
try {
    if ($PSVersionTable.Platform -eq 'Unix') {
        # Unix-like systems
        $volume = Get-PSDrive -Name $Drive -ErrorAction Stop
    } else {
        # Windows systems
        $volume = Get-Volume -ErrorAction Stop | Where-Object { $_.DriveLetter -eq $Drive }
    }

    if ($volume) {
        $total = $volume.Free + $volume.Used
        $percentFree = [int](($volume.Free / $total) * 100)
        Add-Content -Path $logFile -Value "[INFO] Percent Free: $percentFree%"
    } else {
        throw "Drive $Drive was not found."
    }
} catch {
    Add-Content -Path $logFile -Value "[ERROR] Unable to retrieve volume information: $_"
    throw
}

# Notification if below threshold
if ($percentFree -le 20) {
    try {
        Import-Module PoshGram -ErrorAction Stop
        Add-Content -Path $logFile -Value '[INFO] PoshGram imported successfully.'
        $messageSplat = @{
            BotToken    = "YOUR_BOT_TOKEN"
            ChatID      = "YOUR_CHAT_ID"
            Message     = "[LOW SPACE] Drive at: $percentFree%"
            ErrorAction = 'Stop'
        }
        Send-TelegramTextMessage @messageSplat
        Add-Content -Path $logFile -Value '[INFO] Message sent successfully'
    } catch {
        Add-Content -Path $logFile -Value "[ERROR] Error encountered sending message: $_"
        throw
    }
}
```

### Enhancements and Security
- **Secure Handling of Tokens**: Ensure that the Telegram bot token is securely handled, possibly using secure strings or encrypted storage.
- **Parameter Validation**: Adding validation logic for the `$Drive` parameter can help avoid errors due to invalid inputs.
- **Modular Design**: Consider wrapping different functionalities into functions for better readability and reusability.
- **Logging Improvements**: Implement more granular logging levels (INFO, WARN, ERROR) to categorize log entries effectively.

