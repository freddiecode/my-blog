---

title: "Understanding the difference between Get-Module and Get-InstalledModule in PowerShell"
classes: wide
toc: false
date: 2024-06-13 07:00:00
categories:
- Blog
tags:
- PowerShell
- Windows PowerShell
- PowerShell Gallery
---
In PowerShell, both `Get-Module -ListAvailable` and `Get-InstalledModule` are used to retrieve information about modules, but they serve different purposes and functions. Here’s a detailed look at the differences between these two commands and when to use each.

### `Get-Module -ListAvailable`

- **Purpose**: Retrieves a list of all modules available on your system, including those not necessarily installed via PowerShellGet.
- **Function**: Displays modules that are located in the paths listed in `$env:PSModulePath`. This includes modules manually placed, bundled with the operating system, or installed through various means.

**Example**:

````powershell
Get-Module -ListAvailable
````
This command will show all modules that PowerShell can find on your system, regardless of how they were made available.

### `Get-InstalledModule`
- Purpose: Retrieves a list of modules installed using PowerShellGet.
- Function: Displays only those modules installed from the PowerShell Gallery or other registered repositories via PowerShellGet. This includes detailed information such as versions and installation details.

**Example**:

````powershell
Get-InstalledModule
````
This command will list all modules installed through PowerShellGet, providing detailed information such as version, repository name, and more.

### Comparison

| Feature | Get-Module -ListAvailable | Get-InstalledModule |
|----------|----------|----------|
| Scope    | All modules available on the system   | Modules installed through ````PowerShellGet```` |
| Search Paths    | Checks ```$env:PSModulePath```   | Only PowerShellGet-installed modules |
| Module Source    | OS-bundled, manually placed, and ````PowerShellGet````   | Only PowerShell Gallery or registered repositories |
| Use Cases | Finding all possible modules for import | Managing and getting details about PowerShellGet modules | 


### When to Use Which Command?

 - **Get-Module -ListAvailable**:
     * Use when you want to see all available modules on your system.
     * Useful for discovering modules that can be imported, regardless of how they were installed.
     * Handy for debugging and determining local availability.

- **Get-InstalledModule**:
    * Use when you need to manage modules installed from the PowerShell Gallery or other registered repositories.
    * Provides detailed information about installed modules including version and source.
    * Essential for updating and maintaining modules installed via ````PowerShellGet````.

**Examples of Usage**:


````Get-Module -ListAvailable````
This command provides a comprehensive list of all available modules on your system:

````powershell
Get-Module -ListAvailable
````
This will list all modules located in the paths specified in ````$env:PSModulePath````, which is useful for identifying available modules regardless of their installation method.

````Get-InstalledModule````

To get a list of all modules installed via `PowerShellGet`, use:

````powershell
Get-InstalledModule
````

This command gives detailed information about modules installed from the PowerShell Gallery or other registered repositories. For example, to get information about a specific module, you can use:

````powershell
Get-InstalledModule -Name ModuleName
````

### Summary
```Get-Module -ListAvailable```: Use this command to get an overview of all available modules on your system, regardless of how they were installed or placed.

```Get-InstalledModule```: Use this command to manage and get details about modules installed via PowerShellGet, including version and origin information.

By using these commands appropriately, you can better manage the modules in your PowerShell environment and ensure you have access to the necessary tools for your tasks.

Freddie
