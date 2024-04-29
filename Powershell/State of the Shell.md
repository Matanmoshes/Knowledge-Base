# State of the Shell

You're delving into useful commands for determining the PowerShell version you're running and exploring the differences in cmdlet counts between PowerShell versions. Let's discuss these:

1. **Determining PowerShell Version**:
   To find out the version of PowerShell you're running, use `$PSVersionTable`:

   ```powershell
   $PSVersionTable
   ```

   This command shows a table of version-related information for your PowerShell environment. To specifically get the PowerShell version, use:

   ```powershell
   $PSVersionTable.PSVersion
   ```

   This command directly outputs the version number.

2. **Counting Cmdlets in Different PowerShell Versions**:
   - In PowerShell 5.1:
     ```powershell
     Get-Command | Measure-Object
     ```
     This will count the number of cmdlets and functions available in PowerShell 5.1.
   - In PowerShell 6+ (which is cross-platform and a separate product from Windows PowerShell 5.1):
     ```powershell
     Get-Command | Measure-Object
     ```
     You might notice a different count due to the architectural changes and cmdlet updates in PowerShell Core 6+.

3. **Installing the WindowsCompatibility Module in PowerShell 6+**:
   In PowerShell 6+, you can install the `WindowsCompatibility` module to access cmdlets from Windows PowerShell that are not available by default in PowerShell Core. Run:

   ```powershell
   Install-Module WindowsCompatibility -Scope CurrentUser
   ```

   This command installs the `WindowsCompatibility` module for the current user only, and it's particularly useful if you need to use legacy cmdlets that aren't part of the newer PowerShell versions.

Remember, PowerShell 5.1 and PowerShell 6+ (Core) are different versions. PowerShell Core 6+ was a major update with cross-platform support (Windows, Linux, macOS), but some of the Windows-specific cmdlets were not carried over, hence the need for the `WindowsCompatibility` module in certain scenarios.