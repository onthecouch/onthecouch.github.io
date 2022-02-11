---
layout: post
read_time: true
show_date: true
title: "Customizing Powershell Profile."
date: 2021-11-20
img: posts/poshbanner.jpg
tags: [powershell, microsoft, git, profile]
category: powershell
author: Henry Dennis
description: "Microsoft PowerShell is a command-line shell and scripting language designed especially for system administration."
---
In this post, I will show you how to create a PowerShell profile and configure how PowerShell start. [What is PowerShell?](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.2) Read that from the MSDN. I'm not gonna post about what is PowerShell? or something like the history of powershell to make this post looks like a wall of text, while you can read that on official microsoft documentation or just google it.

With the PowerShell profile you can create:
* Style your PowerShell.
* Create aliases.
* Set default variables.
* Import Modules.
* Most important thing is, make your life easy with PowerShell.

Assuming you already have powershell 7 installed on your machine (I'm using Powershell 7), or if not you can [Download PowerShell 7](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.2), choose which one suits you to install it on your machine. 

Later, check if its profile already installed on your windows by running this command as admin.
```powershell
Test-Path $PROFILE
```

If the output is false then run
```powershell
New-Item -Path $profile -Type File -Force
```

Set the Execution Policy to Remote Signed. Otherwise, you won’t be able to run the script when PowerShell opens. Make sure you run PowerShell as admin mode to change the execution policy:
```powershell
Get-ExecutionPolicy

# Set the ExecutionPolicy to RemoteSigned:
Set-ExecutionPolicy RemoteSigned
```
Time to edit your powershell profile. run this command and use whatever editor you liked. (I'm using Visual Studio Code)
```powershell
code $profile
```
### Style your PowerShell.

Customizing the PowerShell looks to your liking, change the background color and settings the font. Below is the profile of mine, feel free to copy paste it. I'm using dracula color scheme because it's look colorful in my boring life.

```powershell
$env:POSH_GIT_ENABLED=$true
Import-Module posh-git
Import-Module -Name Terminal-Icons
Import-Module PSReadLine

# Dracula readline configuration. Requires version 2.0, if you have 1.2 convert to `Set-PSReadlineOption -TokenType`
Set-PSReadlineOption -Color @{
    "Command" = [ConsoleColor]::Green
    "Parameter" = [ConsoleColor]::Gray
    "Operator" = [ConsoleColor]::Magenta
    "Variable" = [ConsoleColor]::White
    "String" = [ConsoleColor]::Yellow
    "Number" = [ConsoleColor]::Blue
    "Type" = [ConsoleColor]::Cyan
    "Comment" = [ConsoleColor]::DarkCyan
}
# Dracula Prompt Configuration
Import-Module posh-git
$GitPromptSettings.DefaultPromptPrefix.Text = "$([char]0x2192) " # arrow unicode symbol
$GitPromptSettings.DefaultPromptPrefix.ForegroundColor = [ConsoleColor]::Green
$GitPromptSettings.DefaultPromptPath.ForegroundColor =[ConsoleColor]::Cyan
$GitPromptSettings.DefaultPromptSuffix.Text = "$([char]0x203A) " # chevron unicode symbol
$GitPromptSettings.DefaultPromptSuffix.ForegroundColor = [ConsoleColor]::Magenta
# Dracula Git Status Configuration
$GitPromptSettings.BeforeStatus.ForegroundColor = [ConsoleColor]::Blue
$GitPromptSettings.BranchColor.ForegroundColor = [ConsoleColor]::Blue
$GitPromptSettings.AfterStatus.ForegroundColor = [ConsoleColor]::Blue


Set-PSReadLineOption -PredictionSource History
Set-PSReadLineOption -PredictionViewStyle ListView
Set-PSReadLineOption -EditMode Windows

# Clipboard interaction is bound by default in Windows mode, but not Emacs mode.
Set-PSReadLineKeyHandler -Key Ctrl+C -Function Copy
Set-PSReadLineKeyHandler -Key Ctrl+v -Function Paste

Set-PSReadLineKeyHandler -Key ')',']','}' `
                         -BriefDescription SmartCloseBraces `
                         -LongDescription "Insert closing brace or skip" `
                         -ScriptBlock {
    param($key, $arg)

    $line = $null
    $cursor = $null
    [Microsoft.PowerShell.PSConsoleReadLine]::GetBufferState([ref]$line, [ref]$cursor)

    if ($line[$cursor] -eq $key.KeyChar)
    {
        [Microsoft.PowerShell.PSConsoleReadLine]::SetCursorPosition($cursor + 1)
    }
    else
    {
        [Microsoft.PowerShell.PSConsoleReadLine]::Insert("$($key.KeyChar)")
    }
}

Set-PSReadLineKeyHandler -Key Backspace `
                         -BriefDescription SmartBackspace `
                         -LongDescription "Delete previous character or matching quotes/parens/braces" `
                         -ScriptBlock {
    param($key, $arg)

    $line = $null
    $cursor = $null
    [Microsoft.PowerShell.PSConsoleReadLine]::GetBufferState([ref]$line, [ref]$cursor)

    if ($cursor -gt 0)
    {
        $toMatch = $null
        if ($cursor -lt $line.Length)
        {
            switch ($line[$cursor])
            {
                <#case#> '"' { $toMatch = '"'; break }
                <#case#> "'" { $toMatch = "'"; break }
                <#case#> ')' { $toMatch = '('; break }
                <#case#> ']' { $toMatch = '['; break }
                <#case#> '}' { $toMatch = '{'; break }
            }
        }

        if ($toMatch -ne $null -and $line[$cursor-1] -eq $toMatch)
        {
            [Microsoft.PowerShell.PSConsoleReadLine]::Delete($cursor - 1, 2)
        }
        else
        {
            [Microsoft.PowerShell.PSConsoleReadLine]::BackwardDeleteChar($key, $arg)
        }
    }
}

# F1 for help on the command line - naturally
Set-PSReadLineKeyHandler -Key F1 `
                         -BriefDescription CommandHelp `
                         -LongDescription "Open the help window for the current command" `
                         -ScriptBlock {
    param($key, $arg)

    $ast = $null
    $tokens = $null
    $errors = $null
    $cursor = $null
    [Microsoft.PowerShell.PSConsoleReadLine]::GetBufferState([ref]$ast, [ref]$tokens, [ref]$errors, [ref]$cursor)

    $commandAst = $ast.FindAll( {
        $node = $args[0]
        $node -is [CommandAst] -and
            $node.Extent.StartOffset -le $cursor -and
            $node.Extent.EndOffset -ge $cursor
        }, $true) | Select-Object -Last 1

    if ($commandAst -ne $null)
    {
        $commandName = $commandAst.GetCommandName()
        if ($commandName -ne $null)
        {
            $command = $ExecutionContext.InvokeCommand.GetCommand($commandName, 'All')
            if ($command -is [AliasInfo])
            {
                $commandName = $command.ResolvedCommandName
            }

            if ($commandName -ne $null)
            {
                Get-Help $commandName -ShowWindow
            }
        }
    }
}
```

Adding those in your profile will make your load of PowerShell takes times. Depending on what modules you have. Talk about modules you see I'm currently using three modules.

**posh git**
Well you know what it is, It's a git module for PowerShell by providing the git status on PowerShell. [View on github](https://github.com/dahlbyk/posh-git) for the installation.

**name terminal icons**
A PowerShell module to show file and folder icons in the terminal. [View on github](https://github.com/devblackops/Terminal-Icons) for the installation.

**PSReadLine**
Replacing command line editing of Powershell. [View on github](https://github.com/PowerShell/PSReadLine) for the installation. Also almost missed it, install [NerdFont](https://www.nerdfonts.com/font-downloads) first before making change to your profile.

![Final Look](./assets/img/posts/posh.jpg)
<small>Final Look </small>