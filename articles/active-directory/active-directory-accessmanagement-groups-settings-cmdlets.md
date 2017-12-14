---
title: "在 Azure Active Directory 中使用 PowerShell 配置组设置 | Microsoft Docs"
description: "如何使用 Azure Active Directory cmdlet 管理组的设置"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 331dafc9164e315c84036fa0af11820e89066f36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>用于配置组设置的 Azure Active Directory cmdlet
本文包含有关使用 Azure Active Directory (Azure AD) PowerShell cmdlet 创建和更新组的说明。 此内容仅适用于 Office 365 组。 

> [!IMPORTANT]
> 某些设置需要 Azure Active Directory Premium P1 许可证。 有关详细信息，请参阅[模板设置](#template-settings)表。

有关如何禁止非管理员用户创建*安全*组的详细信息，请按照 [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) 中的所述设置 `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False`。 

Office365 组设置使用 Settings 对象和 SettingsTemplate 对象配置。 起初，目录中不会显示任何设置对象，因为目录配置为默认设置。 若要更改默认设置，必须使用设置模板创建新的设置对象。 设置模板由 Microsoft 定义。 有几个不同的设置模板。 若要配置目录的 Office 365 组设置，请使用名为“Group.Unified”的模板。 若要针对单个组配置 Office 365 组设置，请使用名为“Group.Unified.Guest”的模板。 此模板用于管理对 Office 365 组的来宾访问权限。 

这些 Cmdlet 属于 Azure Active Directory PowerShell V2 模块。 有关如何在计算机上下载和安装模块的说明，请参阅文章 [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/)（Azure Active Directory PowerShell 版本 2）。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureAD/)安装模块的版本 2 发行版。

## <a name="retrieve-a-specific-settings-value"></a>检索特定的设置值
如果知道要检索的设置的名称，可以使用以下 cmdlet 检索当前的设置值。 在此示例中，我们要检索名为“UsageGuidelinesUrl”的设置的值。 可以在本文的后面部分阅读有关目录设置及其名称的详细信息。

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>在目录级别创建设置
这些步骤在目录级别创建设置，这些设置适用于目录中的所有 Office 365 组（统一组）。

1. 在 DirectorySettings cmdlet 中，必须指定要使用的 SettingsTemplate 的 ID。 如果不知道此 ID，此 cmdlet 将返回所有设置模板的列表：
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  此 cmdlet 调用返回可用的所有模板：
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. 若要添加使用准则 URL，首先需获取定义使用准则 URL 值的 SettingsTemplate 对象，即 Group.Unified 模板：
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. 接下来，创建基于该模板的新设置对象：
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. 然后更新使用准则值：
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. 最后，应用设置：
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

成功完成后，该 cmdlet 返回新设置对象的 ID：
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>模板设置
以下是 Group.Unified SettingsTemplate 中定义的设置。 除非另有说明，否则这些功能都需要 Azure Active Directory Premium P1 许可证。 

| **设置** | **说明** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>类型：布尔值<li>默认值：True |指明是否允许非管理员用户在目录中创建统一组的标志。 此设置不需要 Azure Active Directory Premium P1 许可证。|
|  <ul><li>GroupCreationAllowedGroupId<li>类型：字符串<li>默认值：“” |安全组的 GUID，允许该组的成员创建统一组，即使 EnableGroupCreation == false。 |
|  <ul><li>UsageGuidelinesUrl<li>类型：字符串<li>默认值：“” |组使用准则链接。 |
|  <ul><li>ClassificationDescriptions<li>类型：字符串<li>默认值：“” | 以逗号分隔的分类说明列表。 |
|  <ul><li>DefaultClassification<li>类型：字符串<li>默认值：“” | 如果未指定，则为要用作组的默认分类的分类。|
|  <ul><li>PrefixSuffixNamingRequirement<li>类型：字符串<li>默认值：“” | 请勿使用。 未实现。 |
| <ul><li>CustomBlockedWordsList<li>类型：字符串<li>默认值：“” | 请勿使用。 未实现。 |
| <ul><li>EnableMSStandardBlockedWords<li>类型：布尔值<li>默认值：“False” | 请勿使用
|  <ul><li>AllowGuestsToBeGroupOwner<li>类型：布尔值<li>默认值：False | 一个布尔值，该值指示来宾用户是否可以作为组的所有者。 |
|  <ul><li>AllowGuestsToAccessGroups<li>类型：布尔值<li>默认值：True | 一个布尔值，该值指示来宾用户是否可以访问统一组的内容。  此设置不需要 Azure Active Directory Premium P1 许可证。|
|  <ul><li>GuestUsageGuidelinesUrl<li>类型：字符串<li>默认值：“” | 指向来宾使用指南的链接的 URL。 |
|  <ul><li>AllowToAddGuests<li>类型：布尔值<li>默认值：True | 一个布尔值，该值指示是否允许将来宾添加到此目录。|
|  <ul><li>ClassificationList<li>类型：字符串<li>默认值：“” |一个逗号分隔列表，其中包含的有效分类值可以应用到统一组。 |


## <a name="read-settings-at-the-directory-level"></a>在目录级别读取设置
这些步骤在目录级别读取设置，这些设置适用于目录中的所有 Office 组。

1. 读取所有现有的目录设置：
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  此 cmdlet 返回所有目录设置的列表：
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. 读取特定组的所有设置：
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. 使用设置 ID GUID 读取特定目录设置对象的所有目录设置值：
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  此 cmdlet 返回此特定组的此设置对象中的名称和值：
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>更新特定组的设置

1. 搜索名为“Groups.Unified.Guest”的设置模板
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. 检索 Groups.Unified.Guest 模板的模板对象：
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. 从模板创建新的设置对象：
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. 将设置设为所需的值：
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. 在目录中为所需组创建新设置：
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>在目录级别更新设置

这些步骤在目录级别更新设置，这些设置适用于目录中的所有统一组。 这些示例假定目录中已有 Settings 对象。

1. 查找现有 Settings 对象：
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. 更新值：
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. 更新设置：
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>在目录级别删除设置
这些步骤在目录级别删除设置，这些设置适用于目录中的所有 Office 组。
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet 语法参考
如需更多 Azure Active Directory PowerShell 文档，可参阅 [Azure Active Directory Cmdlet](/powershell/azure/install-adv2?view=azureadps-2.0)。

## <a name="additional-reading"></a>其他阅读材料

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
