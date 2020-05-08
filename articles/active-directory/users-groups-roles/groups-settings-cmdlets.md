---
title: 使用 PowerShell 配置组设置 - Azure AD | Microsoft Docs
description: 如何使用 Azure Active Directory cmdlet 管理组的设置
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/20/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b50118dcd4bf0fafa3e25399cf7d82558b7c776c
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582792"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>用于配置组设置的 Azure Active Directory cmdlet

本文包含有关使用 Azure Active Directory (Azure AD) PowerShell cmdlet 创建和更新组的说明。 此内容仅适用于 Office 365 组（有时称为统一组）。

> [!IMPORTANT]
> 某些设置需要 Azure Active Directory Premium P1 许可证。 有关详细信息，请参阅[模板设置](#template-settings)表。

有关如何防止非管理员用户创建安全组的详细信息，请按照 [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) 中所述内容设置  `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False`。

Office365 组设置使用 Settings 对象和 SettingsTemplate 对象配置。 起初，目录中不会显示任何设置对象，因为目录配置为默认设置。 若要更改默认设置，必须使用设置模板创建新的设置对象。 设置模板由 Microsoft 定义。 有几个不同的设置模板。 若要配置目录的 Office 365 组设置，请使用名为“Group.Unified”的模板。 若要针对单个组配置 Office 365 组设置，请使用名为“Group.Unified.Guest”的模板。 此模板用于管理对 Office 365 组的来宾访问权限。 

这些 Cmdlet 属于 Azure Active Directory PowerShell V2 模块。 有关如何在计算机上下载和安装模块的说明，请参阅文章 [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/)（Azure Active Directory PowerShell 版本 2）。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureAD/)安装模块的版本 2 发行版。

## <a name="install-powershell-cmdlets"></a>安装 PowerShell cmdlet

在运行 PowerShell 命令之前，请确保卸载任何旧版本的 Azure Active Directory PowerShell for Graph、Windows PowerShell 模块，并安装 [Azure Active Directory PowerShell for Graph - 公共预览版（比 2.0.0.137 更新）](https://www.powershellgallery.com/packages/AzureADPreview)。

1. 以管理员身份打开 Windows PowerShell 应用。
2. 卸载任何以前版本的 AzureADPreview。
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. 安装最新版本的 AzureADPreview。
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>在目录级别创建设置
这些步骤在目录级别创建设置，这些设置适用于目录中的所有 Office 365 组。 Get-AzureADDirectorySettingTemplate cmdlet 仅在 [Azure AD PowerShell for Graph 预览模块](https://www.powershellgallery.com/packages/AzureADPreview)中可用。

1. 在 DirectorySettings cmdlet 中，必须指定要使用的 SettingsTemplate 的 ID。 如果不知道此 ID，此 cmdlet 将返回所有设置模板的列表：
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   此 cmdlet 调用返回可用的所有模板：
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. 若要添加使用准则 URL，首先需获取定义使用准则 URL 值的 SettingsTemplate 对象，即 Group.Unified 模板：
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. 接下来，创建基于该模板的新设置对象：
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. 然后更新使用准则值：
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. 然后应用设置：
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. 可以使用以下命令读取值：

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>在目录级别更新设置
若要在设置模板中更新 UsageGuideLinesUrl 的值，请从 Azure AD 读取当前设置，否则我们可能最终会覆盖 UsageGuideLinesUrl 以外的现有设置。

1. 从 Group.Unified SettingsTemplate 获取当前设置：
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. 检查当前设置：
   
   ```powershell
   $Setting.Values
   ```
   
   输出：
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. 若要删除 UsageGuideLinesUrl 的值，请将 URL 编辑为空字符串：
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. 将更新保存到目录：
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>模板设置
以下是 Group.Unified SettingsTemplate 中定义的设置。 除非另有说明，否则这些功能都需要 Azure Active Directory Premium P1 许可证。 

| **设置** | **说明** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>键入：布尔<li>默认值：True |指明是否允许非管理员用户在目录中创建 Office 365 组的标志。 此设置不需要 Azure Active Directory Premium P1 许可证。|
|  <ul><li>GroupCreationAllowedGroupId<li>键入：String<li>默认值："" |安全组的 GUID，允许该组的成员创建 Office 365 组，即使 EnableGroupCreation == false。 |
|  <ul><li>UsageGuidelinesUrl<li>键入：String<li>默认值："" |组使用准则链接。 |
|  <ul><li>ClassificationDescriptions<li>键入：String<li>默认值："" | 以逗号分隔的分类说明列表。 ClassificationDescriptions 的值仅以此格式有效：<br>$setting["ClassificationDescriptions"] ="Classification:Description,Classification:Description"<br>其中，Classification 与 ClassificationList 中的条目匹配。<br>当 EnableMIPLabels == True 时，此设置不适用。|
|  <ul><li>DefaultClassification<li>键入：String<li>默认值："" | 如果未指定，则为要用作组的默认分类的分类。<br>当 EnableMIPLabels == True 时，此设置不适用。|
|  <ul><li>PrefixSuffixNamingRequirement<li>键入：String<li>默认值："" | 最大长度为 64 个字符的字符串，用于定义为 Office 365 组配置的命名约定。 有关详细信息，请参阅[对 Office 365 组强制实施命名策略](groups-naming-policy.md)。 |
| <ul><li>CustomBlockedWordsList<li>键入：String<li>默认值："" | 逗号分隔字符串，用于列出不允许用户在组名称或别名中使用的短语。 有关详细信息，请参阅[对 Office 365 组强制实施命名策略](groups-naming-policy.md)。 |
| <ul><li>EnableMSStandardBlockedWords<li>键入：布尔<li>默认值：“False” | 请勿使用
|  <ul><li>AllowGuestsToBeGroupOwner<li>键入：布尔<li>默认值：False | 一个布尔值，该值指示来宾用户是否可以作为组的所有者。 |
|  <ul><li>AllowGuestsToAccessGroups<li>键入：布尔<li>默认值：True | 一个布尔值，指示来宾用户是否可以访问 Office 365 组的内容。  此设置不需要 Azure Active Directory Premium P1 许可证。|
|  <ul><li>GuestUsageGuidelinesUrl<li>键入：String<li>默认值："" | 指向来宾使用指南的链接的 URL。 |
|  <ul><li>AllowToAddGuests<li>键入：布尔<li>默认值：True | 一个布尔值，该值指示是否允许将来宾添加到此目录。 <br>如果 *EnableMIPLabels* 设置为 *True* 且某个来宾策略与分配给组的敏感性标签相关联，则此设置可能会被重写，变成只读。<br>如果在组织级别将 AllowToAddGuests 设置设置为 False，则将忽略组级别的任何 AllowToAddGuests 设置。 如果你只想为少数几个组启用来宾访问，则必须在组织级别将 AllowToAddGuests 设置为 true，然后有选择地对特定组禁用该访问权限。 |
|  <ul><li>ClassificationList<li>键入：String<li>默认值："" | 一个逗号分隔列表，用于列出可以应用于 Office 365 组的有效分类值。 <br>当 EnableMIPLabels == True 时，此设置不适用。|
|  <ul><li>EnableMIPLabels<li>键入：布尔<li>默认值：“False” |此标记表明在 Microsoft 365 合规中心发布的敏感性标签是否适用于 Office 365 组。 有关详细信息，请参阅为[Office 365 组分配敏感度标签](groups-assign-sensitivity-labels.md)。 |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>示例：在目录级别为组配置来宾策略
1. 获取所有设置模板：
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. 若要在目录级别为组设置来宾策略，需要 Group.Unified 模板
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. 接下来，创建基于该模板的新设置对象：
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. 然后，更新 AllowToAddGuests 设置
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. 然后应用设置：
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. 可以使用以下命令读取值：

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>在目录级别读取设置

如果知道要检索的设置的名称，可以使用以下 cmdlet 检索当前的设置值。 在此示例中，我们要检索名为“UsageGuidelinesUrl”的设置的值。 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
这些步骤在目录级别读取设置，这些设置适用于目录中的所有 Office 组。

1. 读取所有现有的目录设置：
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   此 cmdlet 返回所有目录设置的列表：
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. 读取特定组的所有设置：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. 使用设置 ID GUID 读取特定目录设置对象的所有目录设置值：
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   此 cmdlet 返回此特定组的此设置对象中的名称和值：
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>在目录级别删除设置
这些步骤在目录级别删除设置，这些设置适用于目录中的所有 Office 组。
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>创建特定组的设置

1. 搜索名为“Groups.Unified.Guest”的设置模板
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. 检索 Groups.Unified.Guest 模板的模板对象：
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. 从模板创建新的设置对象：
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. 将设置设为所需的值：
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. 获取要对其应用此设置的组的 ID：
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. 在目录中为所需组创建新设置：
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. 若要验证设置，请运行以下命令：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>更新特定组的设置
1. 获取要更新其设置的组的 ID：
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. 检索组的设置：
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. 根据需要更新组的设置，例如：
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. 然后，获取此特定组的设置的 ID：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   将会获得如下所示的响应：
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. 然后，可以设置此设置的新值：
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. 可以读取此设置的值，确保已将其正确更新：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet 语法参考
如需更多 Azure Active Directory PowerShell 文档，可参阅 [Azure Active Directory Cmdlet](/powershell/azure/install-adv2?view=azureadps-2.0)。

## <a name="additional-reading"></a>其他阅读材料

* [使用 Azure Active Directory 组管理对资源的访问](../fundamentals/active-directory-manage-groups.md)
* [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)
