# PowerShell Input Output
## code

```powershell

#INPUT

#-------------------------------------------------
#cmdlets - obviously

#retrieve dynamic content from a website
$webResults = Invoke-WebRequest -Uri 'https://reddit.com/r/powershell.json'
$rawJSON = $webResults.Content
$objData = $rawJSON | ConvertFrom-Json
$posts = $objData.data.children.data
$posts | Select-Object Title,Score | Sort-Object Score -Descending

#-------------------------------------------------
#Read Host
#https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/read-host?view=powershell-6

[int]$numPosts = Read-Host -Prompt "Enter the number of posts to read"
$posts | Select-Object Title,url | Sort-Object Score -Descending | Select-Object -First $numPosts

[int]$numFacts = Read-Host -Prompt "Enter the number of cat facts you would like"
$webResults = Invoke-RestMethod -Uri "https://catfact.ninja/facts?limit=$numFacts&max_length=140"
$webResults.data

#-------------------------------------------------
#Get Content
#https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-content?view=powershell-6

$logContent = Get-Content C:\Test\SampleLog.log
$logContent = Get-Content C:\Test\SampleLog.log -Tail 5
$b = Get-Content C:\Test\SampleLog.log -Raw

$logContent | Select-String -Pattern "INFO"
$regex = "\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b"
$logContent | Select-String -Pattern $regex -AllMatches
$logContent | Where-Object {$_ -like "*.*.*.*"}

$b | Select-String -Pattern $regex
$b | Where-Object {$_ -like "*.*.*.*"}

#__________________

$rawCSVInput = Get-Content C:\Test\tt.csv
$objData = $rawCSVInput | ConvertFrom-Csv

###############################################################################
###############################################################################

#OUTPUT
#https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/write-host?view=powershell-6
#https://www.jsnover.com/blog/2013/12/07/write-host-considered-harmful/

Write-Host 'Text output to console'
$hostInfo = Get-Host
Write-Host $hostInfo
Write-Host $hostInfo.Version
Write-Host "Warning" -ForegroundColor Yellow
Write-Host "ERROR" -ForegroundColor Red
Write-Host "Works Great" -ForegroundColor Green
Write-Host "CRITICAL ERROR" -BackgroundColor Red -ForegroundColor White

Write-Host "The display name is: $($bits.DisplayName)"

#-------------------------------------------------

Write-Output
#https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/write-output?view=powershell-6

<#
The Write-Output cmdlet sends the specified object down the pipeline to the next command. If the command is the last command in the pipeline, the object is displayed in the console.
Write-Output sends objects down the primary pipeline, also known as the "output stream" or the "success pipeline." To send error objects down the error pipeline, use Write-Error.
This cmdlet is typically used in scripts to display strings and other objects on the console. However, because the default behavior is to display the objects at the end of a pipeline, it is generally not necessary to use the cmdlet. For instance, Get-Process | Write-Output is equivalent to Get-Process.
#>
$processes = Get-Process
Write-Output $processes
$P

#-------------------------------------------------
#Out-File
#https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/out-file?view=powershell-6

$processes = Get-Process
$processes | Out-File C:\Test\processInfo.txt
$processes | ConvertTo-Csv -NoTypeInformation | Out-File c:\test\processes.csv
$processes | Export-Csv -Path c:\test\processes2.csv

$processes = Get-Process
$xml = ConvertTo-Xml -As "Document" -Depth 3 -InputObject $processes

#-------------------------------------------------
```

## Explanation

Your script demonstrates a broad range of PowerShell capabilities for handling input, processing it, and outputting results in various formats. Let’s discuss each part of your script:

### Input
1. **Invoke-WebRequest**:
   - You retrieve JSON content from a website, convert it to a PowerShell object, and then manipulate it. This is a great way to work with APIs and web data in PowerShell.
   ```powershell
   $webResults = Invoke-WebRequest -Uri 'https://reddit.com/r/powershell.json'
   $rawJSON = $webResults.Content
   $objData = $rawJSON | ConvertFrom-Json
   $posts = $objData.data.children.data
   $posts | Select-Object Title,Score | Sort-Object Score -Descending
   ```

2. **Read-Host**:
   - You interact with the user to get input, which you then use to filter and display data.
   ```powershell
   [int]$numPosts = Read-Host -Prompt "Enter the number of posts to read"
   $posts | Select-Object Title,url | Sort-Object Score -Descending | Select-Object -First $numPosts
   ```

3. **Get-Content**:
   - You demonstrate how to read file content, search for patterns with regex, and filter logs, which is essential for file handling and data parsing.
   ```powershell
   $logContent = Get-Content C:\Test\SampleLog.log
   $logContent | Select-String -Pattern "INFO"
   ```

### Output
1. **Write-Host**:
   - This cmdlet is used for displaying data in the console with formatting options. While useful for debugging and scripts that exclusively interact with the console, it is generally avoided in production scripts as it does not output to the PowerShell pipeline.
   ```powershell
   Write-Host "Works Great" -ForegroundColor Green
   ```

2. **Write-Output**:
   - This is the default way PowerShell outputs data. It’s often used explicitly in scripts to make them more readable or when the output needs to be passed explicitly to the next command in the pipeline.
   ```powershell
   $processes = Get-Process
   Write-Output $processes
   ```

3. **Out-File and Export-Csv**:
   - These cmdlets are used to write output to files. `Out-File` writes data to a text file, while `Export-Csv` outputs data in CSV format, which is useful for exporting data that needs to be consumed by other programs or services.
   ```powershell
   $processes | Out-File C:\Test\processInfo.txt
   $processes | Export-Csv -Path c:\test\processes2.csv
   ```

### Summary
Your script effectively showcases how to integrate web APIs, handle user input, read and write files, and display outputs in PowerShell. Each of these operations is fundamental in PowerShell scripting, especially for tasks involving automation and data manipulation.
