---
title: Find in Files Command
description: Learn about the Find command and how it searches files using some of the options available on the Find in Files tab of the Find and Replace window.
ms.custom: SEO-VS-2020
ms.date: 01/14/2022
ms.topic: reference
f1_keywords:
- edit.findinfiles
helpviewer_keywords:
- Edit.FindInFiles command
- find in files command
ms.assetid: 2fc78bfe-b339-4599-97f9-4cafd8a194d9
author: TerryGLee
ms.author: tglee
manager: jmartens
ms.technology: vs-ide-general
ms.workload:
- multiple
monikerRange: "<=vs-2019"
---
# Find in Files Command

 [!INCLUDE [Visual Studio](~/includes/applies-to-version/vs-windows-only.md)]
Search files using a subset of the options available on the **Find in Files** tab of the **Find and Replace** window.

> [!IMPORTANT]
> Support for this command was discontinued in version 16.5 of Visual Studio 2019. If you use this command with version 16.5 or a later version, you might see an error message that says, *Command "Edit.FindinFiles" does not accept arguments or switches.*

## Syntax

```cmd
Edit.FindinFiles findwhat [/case] [/ext:extensions]
[/lookin:searchpath] [/names] [/options] [/reset] [/stop] [/sub]
[/text2] [/wild|/regex] [/word]
```

## Arguments

`findwhat`\
Required. The text to match.

## Switches
/case or /c\
Optional. Matches occur only if the uppercase and lowercase characters exactly match those specified in the `findwhat` argument.

/ext: `extensions`\
Optional. Specifies the file extensions for the files to be searched. If not specified, the previous extension is used if one was previously entered.

/lookin: `searchpath`\
Optional. Directory to search. If the path contains spaces, enclose the entire path in quotation marks.

/names or /n\
Optional. Displays a list of file names that contain matches.

/options or /t\
Optional. Displays a list of the current find option settings and does not perform a search.

/regex or /r\
Optional. Uses pre-defined special characters in the `findwhat` argument as notations that represent patterns of text rather than the literal characters. For a complete list of regular expression characters, see [Regular Expressions](../../ide/using-regular-expressions-in-visual-studio.md).

/reset or /e\
Optional. Returns the find options to their default settings and does not perform a search.

/stop\
Optional. Halts the current search operation if one is in progress. Search ignores all other arguments when `/stop` has been specified. For example, to stop the current search you would enter the following:

```cmd
>Edit.FindinFiles /stop
```

/sub or /s\
Optional. Searches the subfolders within the directory specified in the /lookin:`searchpath` argument.

/text2 or /2\
Optional. Displays the results of the search in the Find Results 2 window.

/wild or /l\
Optional. Uses pre-defined special characters in the `findwhat` argument as notations to represent a character or sequence of characters.

/word or /w\
Optional. Searches only for whole words.

## Example
This example searches for btnCancel in all .cls files located in the folder "My Visual Studio Projects" and displays the match information in the Find Results 2 Window.

```cmd
>Edit.FindinFiles btnCancel /lookin:"c:/My Visual Studio Projects" /ext:*.cls /text2
```

## See also

- [Find in Files](../../ide/find-in-files.md)
- [Command Window](../../ide/reference/command-window.md)
- [Find/Command Box](../../ide/find-command-box.md)
- [Visual Studio Commands](../../ide/reference/visual-studio-commands.md)
- [Visual Studio Command Aliases](../../ide/reference/visual-studio-command-aliases.md)
