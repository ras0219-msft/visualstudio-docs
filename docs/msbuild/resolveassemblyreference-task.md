---
title: ResolveAssemblyReference Task | Microsoft Docs
description: Learn how MSBuild uses the ResolveAssemblyReference task to determine all assemblies that depend on the specified assemblies.
ms.custom: SEO-VS-2020
ms.date: 11/04/2016
ms.topic: reference
f1_keywords:
- http://schemas.microsoft.com/developer/msbuild/2003#ResolveAssemblyReference
- MSBuild.ResolveAssemblyReference.TurnOnAutoGenerateBindingRedirects
- MSBuild.ResolveAssemblyReference.FoundConflict
- MSBuild.ResolveAssemblyRedirects.SuggestedRedirects
dev_langs:
- VB
- CSharp
- C++
- jsharp
helpviewer_keywords:
- ResolveAssemblyReference task [MSBuild]
- MSBuild, ResolveAssemblyReference task
ms.assetid: 4d56d848-b29b-4dff-86a2-0a96c9e4a170
author: ghogen
ms.author: ghogen
manager: jmartens
ms.technology: msbuild
ms.workload:
- multiple
---
# ResolveAssemblyReference task

Determines all assemblies that depend on the specified assemblies, including second and `n`th-order dependencies.

## Parameters

 The following table describes the parameters of the `ResolveAssemblyReference` task.

|Parameter|Description|
|---------------|-----------------|
|`AllowedAssemblyExtensions`|Optional `String[]` parameter.<br /><br /> The assembly file name extensions to use when resolving references. The default file name extensions are *.exe* and *.dll*.|
|`AllowedRelatedFileExtensions`|Optional `String[]` parameter.<br /><br /> The file name extensions to use for a  search for files that are related to one another. The default extensions are *.pdb* and *.xml*.|
|`AppConfigFile`|Optional `String` parameter.<br /><br /> Specifies an *app.config* file from which to parse and extract bindingRedirect mappings. If this parameter is specified, the `AutoUnify` parameter must be `false`.|
|`Assemblies`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` parameter.<br /><br /> Specifies the items for which full paths and dependencies must be identified. These items can have either simple names like "System" or strong names like "System, Version=2.0.3500.0, Culture=neutral, PublicKeyToken=b77a5c561934e089".<br /><br /> Items passed to this parameter may optionally have the following item metadata:<br /><br /> -   `Private`: `Boolean` value. If `true`, then the item is copied locally. The default value is `true`.<br />-   `HintPath`: `String` value. Specifies the path and file name to use as a reference. This metadata is used when {HintPathFromItem} is specified in the `SearchPaths` parameter. The default value is an empty string.<br />-   `SpecificVersion`: `Boolean` value. If `true`, then the exact name specified in the `Include` attribute must match. If `false`, then any assembly with the same simple name will work. If `SpecificVersion` is not specified, then the task examines the value in the `Include` attribute of the item. If the attribute is a simple name, it behaves as if `SpecificVersion` was `false`. If the attribute is a strong name, it behaves as if `SpecificVersion` was `true`.<br />     When used with a Reference item type, the `Include` attribute needs to be the full fusion name of the assembly to be resolved. The assembly is only resolved if fusion exactly matches the `Include` attribute.<br />     When a project targets a .NET Framework version and references an assembly compiled for a higher .NET Framework version, the reference resolves only if it has `SpecificVersion` set to `true`.<br />     When a project targets a profile and references an assembly that is not in the profile, the reference resolves only if it has `SpecificVersion` set to `true`.<br />-   `ExecutableExtension`: `String` value. When present, the resolved assembly must have this extension. When absent, *.dll* is considered first, followed by *.exe*, for each examined directory.<br />-   `SubType`: `String` value. Only items with empty SubType metadata will be resolved into full assembly paths. Items with non-empty SubType metadata are ignored.<br />-   `AssemblyFolderKey`: `String` value. This metadata is supported for legacy purposes. It specifies a user-defined registry key, such as **hklm\\\<VendorFolder>**, that `Assemblies` should use to resolve assembly references.|
|`AssemblyFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` parameter.<br /><br /> Specifies a list of fully qualified assemblies for which to find dependencies.<br /><br /> Items passed to this parameter may optionally have the following item metadata:<br /><br /> -   `Private`: an optional `Boolean` value. If true, the item is copied locally.<br />-   `FusionName`: optional `String` metadata. Specifies the simple or strong name for this item. If this attribute is present, it can save time because the assembly file does not have to be opened to get the name.|
|`AutoUnify`|Optional `Boolean` parameter.<br /><br /> This parameter is used for building assemblies, such as DLLs, which cannot have a normal *App.Config* file.<br /><br /> When `true`, the resulting dependency graph is automatically treated as if there were an *App.Config* file passed in to the AppConfigFile parameter. This virtual *App.Config* file has a bindingRedirect entry for each conflicting set of assemblies such that the highest version assembly is chosen. A consequence of this is that there will never be a warning about conflicting assemblies, because every conflict will have been resolved.<br /><br /> When `true`, each distinct remapping will result in a high priority comment showing the old and new versions and that `AutoUnify` was `true`.<br /><br /> When `true`, the AppConfigFile parameter must be empty.<br /><br /> When `false`, no assembly version remapping will occur automatically. When two versions of an assembly are present, a warning is issued.<br /><br /> When `false`, each distinct conflict between different versions of the same assembly results in a high-priority comment. These comments are followed by a single warning. The warning has a unique error code and contains text that reads "Found conflicts between different versions of reference and dependent assemblies".<br /><br /> The default value is `false`.|
|`CandidateAssemblyFiles`|Optional `String[]` parameter.<br /><br /> Specifies a list of assemblies to use for the search and resolution process. Values passed to this parameter must be absolute file names or project-relative file names.<br /><br /> Assemblies in this list will be considered when the `SearchPaths` parameter contains {CandidateAssemblyFiles} as one of the paths to consider.|
|`CopyLocalDependenciesWhenParentReferenceInGac`|Optional <xref:System.Boolean> parameter.<br /><br /> If true, to determine if a dependency should be copied locally, one of the checks done is to see if the parent reference in the project file has the Private metadata set. If set, then the Private value is used as a dependency.<br /><br /> If the metadata is not set, then the dependency goes through the same checks as the parent reference. One of these checks is to see if the reference is in the GAC. If a reference is in the GAC, then it is not copied locally, because it is assumed to be in the GAC on the target machine. This only applies to a specific reference and not its dependencies.<br /><br /> For example, a reference in the project file that is in the GAC is not copied locally, but its dependencies are copied locally because they are not in the GAC.<br /><br /> If false, project file references are checked to see if they are in the GAC, and are copied locally as appropriate.<br /><br /> Dependencies are checked to see if they are in the GAC and are also checked to see if the parent reference from the project file is in the GAC.<br /><br /> If the parent reference from the project file is in the GAC, the dependency is not copied locally.<br /><br /> Whether this parameter is true or false, if there are multiple parent references and any of them are not in the GAC, then all of them are copied locally.|
|`CopyLocalFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Returns every file in the `ResolvedFiles`, `ResolvedDependencyFiles`, `RelatedFiles`, `SatelliteFiles`, and `ScatterFiles` parameters that has `CopyLocal` item metadata with a value of `true`.|
|`FilesWritten`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` output parameter.<br /><br /> Contains the items written to disk.|
|`FindDependencies`|Optional `Boolean` parameter.<br /><br /> If `true`, dependencies will be found. Otherwise, only primary references are found. The default value is `true`.|
|`FindRelatedFiles`|Optional `Boolean` parameter.<br /><br /> If `true`, related files such as *.pdb* files and *xml* files will be found. The default value is `true`.|
|`FindSatellites`|Optional `Boolean` parameter.<br /><br /> If `true`, satellite assemblies will be found. The default value is `true.`|
|`FindSerializationAssemblies`|Optional `Boolean` parameter.<br /><br /> If `true`, then the task searches for serialization assemblies. The default value is `true`.|
|`FullFrameworkAssemblyTables`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` parameter.<br /><br /> Specifies items that have "FrameworkDirectory" metadata to associate a redist list with a particular framework directory. If the association is not made, an error will be logged. The resolve assembly reference (RAR) logic uses the target framework directory if a FrameworkDirectory is not set.|
|`FullFrameworkFolders`|Optional <xref:System.String?displayProperty=fullName>`[]` parameter.<br /><br /> Specifies the folders that contain a RedistList directory. This directory represents the full framework for a given client profile, for example, *%programfiles%\reference assemblies\microsoft\framework\v4.0*.|
|`FullTargetFrameworkSubsetNames`|Optional `String[]` parameter.<br /><br /> Contains a list of target framework subset names. If a subset name in the list matches one in the `TargetFrameworkSubset` name property, then the system excludes that particular target framework subset at build time.|
|`IgnoreDefaultInstalledAssemblyTables`|Optional `Boolean` parameter.<br /><br /> If `true`, then the task searches for and uses additional installed assembly tables (or, "Redist Lists") that are found in the *\RedistList* directory under `TargetFrameworkDirectories`. The default value is `false.`|
|`IgnoreDefaultInstalledAssemblySubsetTables`|Optional `Boolean` parameter.<br /><br /> If `true`, then the task searches for and uses additional installed assembly subset tables (or, "Subset Lists") that are found in the *\SubsetList* directory under `TargetFrameworkDirectories`. The default value is `false.`|
 |`IgnoreTargetFrameworkAttributeVersionMismatch `|Optional `Boolean` parameter.<br /><br /> If `true`, then the task will resolve assemblies that target a higher .NET Framework version than the current project. The default value is `false`, which will skip those references.|
|`InstalledAssemblySubsetTables`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` parameter.<br /><br /> Contains a list of XML files that specify the assemblies that are expected to be in the target subset.<br /><br /> As an option, items in this list can specify the "FrameworkDirectory" metadata to associate an `InstalledAssemblySubsetTable`<br /><br /> with a particular framework directory.<br /><br /> If there is only one `TargetFrameworkDirectories` element, then any items in this list that lack the "FrameworkDirectory" metadata are treated as though they are set to the unique value that is passed to `TargetFrameworkDirectories`.|
|`InstalledAssemblyTables`|Optional `String` parameter.<br /><br /> Contains a list of XML files that specify the assemblies that are expected to be installed on the target computer.<br /><br /> When `InstalledAssemblyTables` is set, earlier versions of the assemblies in the list are merged into the newer versions that are listed in the XML. Also, assemblies that have a setting of InGAC='true' are considered prerequisites and are set to CopyLocal='false' unless explicitly overridden.<br /><br /> As an option, items in this list can specify "FrameworkDirectory" metadata to associate an `InstalledAssemblyTable` with a particular framework directory.  However, this setting is ignored unless the Redist name begins with<br /><br /> "Microsoft-Windows-CLRCoreComp".<br /><br /> If there is only one `TargetFrameworkDirectories` element, then any items in this list that lack the "FrameworkDirectory" metadata are treated as if they are set to the unique value that is passed<br /><br /> to `TargetFrameworkDirectories`.|
|`LatestTargetFrameworkDirectories`|Optional `String[]` parameter.<br /><br /> Specifies a list of directories that contain the redist lists for the most current framework that can be targeted on the machine. If this is not set, then the highest framework installed on the machine for a given target framework identifier is used.|
|`ProfileName`|Optional `String` parameter.<br /><br /> -   Specifies the name of the framework profile to be targeted. For example, Client, Web, or Network.|
|`RelatedFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Contains related files, such as XML and *.pdb* files that have the same base name as a reference.<br /><br /> The files listed in this parameter may optionally contain the following item metadata:<br /><br /> -   `Primary`: `Boolean` value. If `true`, then the file item was passed into the array by using the `Assemblies` parameter. Default value is `false`.<br />-   `CopyLocal`: `Boolean` value. Indicates whether the given reference should be copied to the output directory.|
|`ResolvedDependencyFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Contains the *n*th order paths to dependencies. This parameter does not include first order primary references, which are contained in the `ResolvedFiles` parameter.<br /><br /> The items in this parameter optionally contain the following item metadata:<br /><br /> -   `CopyLocal`: `Boolean` value. Indicates whether the given reference should be copied to the output directory.<br />-   `FusionName`: `String` value. Specifies the name for this dependency.<br />-   `ResolvedFrom`: `String` value. Specifies the literal search path that this file was resolved from.|
|`ResolvedFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Contains a list of all primary references resolved to full paths.<br /><br /> The items in this parameter optionally contain the following item metadata:<br /><br /> -   `CopyLocal`: `Boolean` value. Indicates whether the given reference should be copied to the output directory.<br />-   `FusionName`: `String` value. Specifies the name for this dependency.<br />-   `ResolvedFrom`: `String` value. Specifies the literal search path that this file was resolved from.|
|`SatelliteFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Specifies any satellite files found. These will be CopyLocal=true if the reference or dependency that caused this item to exist is CopyLocal=true.<br /><br /> The items in this parameter optionally contain the following item metadata:<br /><br /> -   `CopyLocal`: `Boolean` value. Indicates whether the given reference should be copied to the output directory. This value is `true` if the reference or dependency that caused this item to exist has a `CopyLocal` value of `true`.<br />-   `DestinationSubDirectory`: `String` value. Specifies the relative destination directory to copy this item to.|
|`ScatterFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Contains the scatter files associated with one of the given assemblies.<br /><br /> The items in this parameter optionally contain the following item metadata:<br /><br /> -   `CopyLocal`: `Boolean` value. Indicates whether the given reference should be copied to the output directory.|
|`SearchPaths`|Required `String[]` parameter.<br /><br /> Specifies the directories or special locations that are searched to find the files on disk that represent the assemblies. The order in which the search paths are listed is important. For each assembly, the list of paths is searched from left to right. When a file that represents the assembly is found, that search stops and the search for the next assembly starts.<br /><br /> This parameter accepts a semicolon-delimited list of values that can be either directory paths or special literal values from the list below:<br /><br /> -   `{HintPathFromItem}`: Specifies that the task will examine the `HintPath` metadata of the base item.<br />-   `{CandidateAssemblyFiles}`: Specifies that the task will examine the files passed in through the `CandidateAssemblyFiles` parameter.<br />-   `{Registry:` \<AssemblyFoldersBase>, \<RuntimeVersion>, \<AssemblyFoldersSuffix>`}`: Specifies that the task will search in additional folders specified in the registry. \<AssemblyFoldersBase>, \<RuntimeVersion>, and \<AssemblyFoldersSuffix> should be replaced with specific values for the registry location to be searched. The default specification in the common targets is {Registry:$(FrameworkRegistryBase), $(TargetFrameworkVersion), $(AssemblyFoldersSuffix), $(AssemblyFoldersExConditions)}.<br />-   `{AssemblyFolders}`: Specifies the task will use the Visual Studio.NET 2003 finding-assemblies-from-registry scheme.<br />-   `{GAC}`: Specifies the task will search in the Global Assembly Cache (GAC).<br />-   `{RawFileName}`: Specifies the task will consider the `Include` value of the item to be an exact path and file name.|
|`SerializationAssemblyFiles`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Contains any XML serialization assemblies found. These items are marked CopyLocal=true if and only if the reference or dependency that caused this item to exist is CopyLocal=true.<br /><br /> The `Boolean` metadata CopyLocal indicates whether the given reference should be copied to the output directory.|
|`Silent`|Optional `Boolean` parameter.<br /><br /> If `true`, no messages are logged. The default value is `false`.|
|`StateFile`|Optional `String` parameter.<br /><br /> Specifies a file name that indicates where to save the intermediate build state for this task.|
|`SuggestedRedirects`|Optional <xref:Microsoft.Build.Framework.ITaskItem>`[]` read-only output parameter.<br /><br /> Contains one item for every distinct conflicting assembly identity, regardless of the value of the `AutoUnify` parameter. This includes every culture and PKT that was found that did not have a suitable bindingRedirect entry in the application configuration file.<br /><br /> Each item optionally contains the following information:<br /><br /> -   `Include` attribute: Contains the full name of the assembly family with a Version field value of 0.0.0.0<br />-   `MaxVersion` item metadata: Contains the maximum version number.|
|`TargetedRuntimeVersion`|Optional `String` parameter.<br /><br /> Specifies the runtime version to target, for example, 2.0.57027 or v2.0.57027.|
|`TargetFrameworkDirectories`|Optional `String[]` parameter.<br /><br /> Specifies the path of the target framework directory. This parameter is required to determine the CopyLocal status for resulting items.<br /><br /> If this parameter is not specified, no resulting items will have a CopyLocal value of `true` unless they explicitly have a `Private` metadata value of `true` on their source item.|
|`TargetFrameworkMoniker`|Optional `String` parameter.<br /><br /> The TargetFrameworkMoniker to monitor, if any. This is used for logging.|
|`TargetFrameworkMonikerDisplayName`|Optional `String` parameter.<br /><br /> The display name of the TargetFrameworkMoniker to monitor, if any. This is used for logging.|
|`TargetFrameworkSubsets`|Optional `String[]` parameter.<br /><br /> Contains a list of target framework subset names to be searched for in the target framework directories.|
|`TargetFrameworkVersion`|Optional `String` parameter.<br /><br /> The project target framework version. The default value is empty, which means there is no filtering for the references based on target framework.|
|`TargetProcessorArchitecture`|Optional `String` parameter.<br /><br /> The preferred target processor architecture. Used for resolving Global Assembly Cache (GAC) references.<br /><br /> This parameter can have a value of `x86`, `IA64`, or `AMD64`.<br /><br /> If this parameter is absent, the task first considers assemblies that match the architecture of the currently running process. If no assembly is found, the task considers assemblies in the GAC that have `ProcessorArchitecture` value of `MSIL` or no `ProcessorArchitecture` value.|

## Warnings

 The following warnings are logged:

- `ResolveAssemblyReference.TurnOnAutoGenerateBindingRedirects`

- `ResolveAssemblyReference.SuggestedRedirects`

- `ResolveAssemblyReference.FoundConflicts`

- `ResolveAssemblyReference.AssemblyFoldersExSearchLocations`

- `ResolveAssemblyReference.UnifiedPrimaryReference`

- `ResolveAssemblyReference.PrimaryReference`

- `ResolveAssemblyReference.UnifiedDependency`

- `ResolveAssemblyReference.UnificationByAutoUnify`

- `ResolveAssemblyReference.UnificationByAppConfig`

- `ResolveAssemblyReference.UnificationByFrameworkRetarget`

## Remarks

 In addition to the parameters listed above, this task inherits parameters from the <xref:Microsoft.Build.Tasks.TaskExtension> class, which itself inherits from the <xref:Microsoft.Build.Utilities.Task> class. For a list of these additional parameters and their descriptions, see [TaskExtension base class](../msbuild/taskextension-base-class.md).

## See also

- [Tasks](../msbuild/msbuild-tasks.md)
- [Task reference](../msbuild/msbuild-task-reference.md)
