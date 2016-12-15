---
title: "用于配置组设置的 Azure Active Directory cmdlet | Microsoft Docs"
description: "如何使用 Azure Active Directory cmdlet 管理组的设置。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6a8bd076830d9b639007ee0130320869d2a63746


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>用于配置组设置的 Azure Active Directory cmdlet
可以在目录中配置统一组的以下设置：

1. 分类：用户可以对组进行设置的分类的逗号分隔列表。 例如，“已分类”、“机密”和“最高机密”。
2. 使用准则 URL：此 URL 指导用户在使用统一组时遵守组织定义的使用条款。 此 URL 会显示在方便用户使用组的用户界面中。
3. 允许创建组：是允许所有用户、部分用户还是不允许用户创建统一组。 设置为 on 时，所有用户都可以创建组。 设置为 off 时，没有用户可以创建组。 设置为 off 时，也可指定一个安全组，允许其用户创建组。

这些设置使用 Settings 和 SettingsTemplate 对象配置。 一开始不会在目录中看到任何 Settings 对象。 这意味着目录配置了默认设置。 若要更改默认设置，需使用设置模板创建新的设置对象。 设置模板由 Microsoft 定义。

可以从 [Microsoft Connect 站点](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)下载模块，其中包含用于这些操作的 cmdlet。

## <a name="create-settings-at-the-directory-level"></a>在目录级别创建设置
这些步骤在目录级别创建设置，这些设置适用于目录中的所有 Office 组。

1. 如果不知道使用哪个 SettingTemplate，可通过以下 cmdlet 返回设置模板列表：
   
    `Get-MsolAllSettingTemplate`
   
    ![设置模板列表](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)
2. 若要添加使用准则 URL，首先需获取定义使用准则 URL 值的 SettingsTemplate 对象，即 Group.Unified 模板：
   
    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`
3. 接下来，创建基于该模板的新设置对象：
   
    `$setting = $template.CreateSettingsObject()`
4. 然后更新使用准则值：
   
    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`
5. 最后，应用设置：
   
    `New-MsolSettings –SettingsObject $setting`
   
    ![添加使用准则 URL](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

以下是 Group.Unified SettingsTemplate 中定义的设置。

| **设置** | **说明** |
| --- | --- |
|  <ul><li>ClassificationList<li>类型：字符串<li>默认值：“” |一个逗号分隔列表，其中包含的有效分类值可以应用到统一组。 |
|  <ul><li>EnableGroupCreation<li>类型：布尔值<li>默认值：True |一个标志，指示是否允许在目录中创建统一组。 |
|  <ul><li>GroupCreationAllowedGroupId<li>类型：字符串<li>默认值：“” |安全组的 GUID，该安全组可以创建统一组，即使 EnableGroupCreation == false。 |
|  <ul><li>UsageGuidelinesUrl<li>类型：字符串<li>默认值：“” |组使用准则链接。 |

## <a name="read-settings-at-the-directory-level"></a>在目录级别读取设置
这些步骤在目录级别读取设置，这些设置适用于目录中的所有 Office 组。

1. 读取所有现有的目录设置：
   
    `Get-MsolAllSettings`
2. 读取特定组的所有设置：
   
    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`
3. 使用 SettingId GUID 读取特定目录设置：
   
    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
   
    ![设置 ID GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>在目录级别更新设置
这些步骤在目录级别更新设置，这些设置适用于目录中的所有 Office 组。

1. 获取现有 Settings 对象：
   
    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
2. 获取要更新的值：
   
    `$value = $Setting.GetSettingsValue()`
3. 更新值：
   
    `$value["AllowToAddGuests"] = "false"`
4. 更新设置：
   
    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>在目录级别删除设置
这些步骤在目录级别删除设置，这些设置适用于目录中的所有 Office 组。

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Cmdlet 语法参考
如需更多 Azure Active Directory PowerShell 文档，可参阅 [Azure Active Directory Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=808260)。

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>SettingsTemplate 对象引用（Group.Unified SettingsTemplate 对象）
* "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "一个布尔标志，指示是否启用了统一组创建功能。"
* "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "安全组的 GUID，该安全组可以创建统一组。"
* "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "一个逗号分隔列表，其中包含的有效分类值可以应用到统一组。"
* "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "组使用准则链接。"

| name | type | defaultValue | description |
| --- | --- | --- | --- |
| "EnableGroupCreation" |"System.Boolean" |"true" |"一个布尔标志，指示是否启用了统一组创建功能。" |
| "GroupCreationAllowedGroupId" |"System.Guid" |"" |"安全组的 GUID，该安全组可以创建统一组。" |
| "ClassificationList" |"System.String" |"" |"一个逗号分隔列表，其中包含的有效分类值可以应用到统一组。" |
| "UsageGuidelinesUrl" |"System.String" |"" |"组使用准则链接。" |

## <a name="next-steps"></a>后续步骤
如需更多 Azure Active Directory PowerShell 文档，可参阅 [Azure Active Directory Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=808260)。

如需 Microsoft 项目经理 Rob de Jong 的更多指导性文章，请访问 [Rob's Groups Blog](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad)（Rob 的组博客）。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)




<!--HONumber=Nov16_HO3-->


