# PowerShell Variables
## code

```powershell

#https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_variables?view=powershell-6
#https://docs.microsoft.com/en-us/powershell/scripting/learn/using-variables-to-store-objects?view=powershell-6
#____________________________________________________________
Get-Process
$processes = Get-Process
$processes
#----------------------------------------
# not using variable
Get-Process | Where-Object { $_.CPU -gt 5000 } #find processes keeping the CPU busy
Get-Process | Sort-Object WorkingSet64 -Descending #sort processes by memory usage
#----------------------------------------
# using variable
$processes = Get-Process
$processes | Where-Object { $_.CPU -gt 5000 } #find processes keeping the CPU busy
$processes | Sort-Object WorkingSet64 -Descending #sort processes by memory usage
#----------------------------------------
# not strong typed
$myNewVariable

# data types
#----------------------------------------
$total = 2 + 2
$total
$total | Get-Member
#----------------------------------------
$total = '2 + 2'
$total
$total | Get-Member
#____________________________________________________________
# understanding data types

$num1 = 2
$num2 = 2
$total = $num1 + $num2
$total
#----------------------------------------
$num1 = '2'
$num2 = '2'
$total = $num1 + $num2
$total
#----------------------------------------

#strong type
#----------------------------------------
[int]$num1 = '2'
[int]$num2 = '2'
$total = $num1 + $num2
#----------------------------------------

#convert
#----------------------------------------
$stringReturn = $total.ToString()
$total | Get-Member
#----------------------------------------

#quotes
$literal = 'Two plus one equals: $(1 + 2)'
$literal
$escaped = "Two plus one equals: $(1 + 2)"
$escaped
Write-Host '$escaped'
Write-Host "$escaped"

#constant variables are reserved
Get-Variable
# you can't use these
$HOME = 'c:\test'

#environment variables
Get-ChildItem env:
$env:COMPUTERNAME
$env:USERNAME

#putting it all together with an example
$path = Read-Host -Prompt 'Please enter the file path you wish to scan for large files...'
$rawFileData = Get-ChildItem -Path $path -Recurse
$largeFiles = $rawFileData | Where-Object { $_.Length -gt 100MB }
$largeFilesCount = $largeFiles | Measure-Object | Select-Object -ExpandProperty Count
Write-Host "You have $largeFilesCount large file(s) in $path"
#____________________________________________________________
```
---
## Explanation

Your script includes a wide range of PowerShell concepts, from handling variables and data types to using cmdlets for system information and file management. Let's break down these concepts and examine the different parts of your script:

### Basic Cmdlets and Variables
- `Get-Process` retrieves all currently running processes.
- `$processes = Get-Process` stores the output of `Get-Process` in the variable `$processes` for further use, reducing the need to repeatedly call `Get-Process`.

### Using Variables with Cmdlets
- Variables like `$processes` are used with pipeline operations to filter or sort data. For example:
  - `$processes | Where-Object { $_.CPU -gt 5000 }` finds processes using more than 5000 CPU units.
  - `$processes | Sort-Object WorkingSet64 -Descending` sorts processes by memory usage in descending order.

### Understanding Data Types
- PowerShell is not strongly typed by default, meaning you can assign different types of data to the same variable without explicit declarations.
- **Example with integers**:
  - `$num1 = 2; $num2 = 2; $total = $num1 + $num2` results in `$total` being `4`.
- **Example with strings**:
  - `$num1 = '2'; $num2 = '2'; $total = $num1 + $num2` results in `$total` being `'22'`, illustrating string concatenation instead of arithmetic addition.

### Strong Typing
- You can enforce data types by declaring a type:
  - `[int]$num1 = '2'` ensures `$num1` is treated as an integer.

### Conversions
- Converting data types is straightforward:
  - `$stringReturn = $total.ToString()` converts `$total` to a string.
- The `Get-Member` cmdlet reveals the methods and properties associated with a variable or object.

### String Interpolation and Quotes
- Single quotes (`'`) are used for literals.
- Double quotes (`"`) are used for interpolation (expanding variables and expressions within strings).
- **Example**:
  - `$literal = 'Two plus one equals: $(1 + 2)'` does not expand the expression.
  - `$escaped = "Two plus one equals: $(1 + 2)"` evaluates the expression, displaying "Two plus one equals: 3".

### Environment Variables and Constants
- Environment variables like `$env:COMPUTERNAME` can be accessed directly.
- Constants like `$HOME` should not be reassigned as they are reserved.

### Practical Application Example
- The script prompts for a file path and scans for large files (greater than 100MB), counting them and displaying the count.
  - `Read-Host` captures user input.
  - `Get-ChildItem -Path $path -Recurse` retrieves files and directories.
  - The files are filtered and counted, and the results are displayed using `Write-Host`.

This example demonstrates powerful ways to use PowerShell for automation and system management tasks, highlighting its flexibility and capability in handling various data types and operations seamlessly. 
