<properties
   pageTitle="Azure Active Directory 审核报告事件 | Microsoft Azure"
   description="可从 Azure Active Directory 查看和下载的已审核事件"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
 
   ms.date="03/07/2016"
   wacn.date=""/>

# Azure Active Directory 审核报告事件

本文档是 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)的一部分。

Azure Active Directory 审核报告可帮助客户识别其 Azure Active Directory 中发生的特权操作。特权操作包括提升更改（例如，创建角色或密码重置）、更改策略配置（例如密码策略）或更改目录配置（例如，对域联合身份验证设置的更改）。报告提供了事件名称的审核记录、操作执行者、更改影响的目标资源，以及日期和时间 (UTC)。客户可以根据 [View your access and usage reports（查看访问和使用情况报告）](active-directory-view-access-usage-reports.md)中所述，通过 [Azure 管理门户](https://manage.windowsazure.com/)检索其 Azure Active Directory 的审核事件列表。


## 审核报告事件列表
<!--- audit event descriptions should be in the past tense --->

事件 | 事件说明
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**用户事件** |
添加用户 | 已将用户添加到目录。
删除用户 | 已从目录中删除用户。
设置许可证属性 | 已设置目录中用户的许可证属性。
重置用户密码 | 已重置目录中用户的密码。
更改用户密码 | 已更改目录中用户的密码。
更改用户许可证 | 已更改分配给目录中用户的许可证。若要查看哪些许可证已更新，请查看紧接在此事件前面或后面的“更新用户”事件。
更新用户 | 已更新目录中的用户。有关可更新的属性，请[参阅下文](#quotupdate-userquot-attributes)。
设置强制更改用户密码 | 已设置强制用户在登录时更改其密码的属性。
**组事件** |
添加组 | 已在目录中创建组。
更新组 | 已在目录中更新组。
删除组 | 已从目录中删除组。
将成员添加到组 | 已将成员添加到目录中的组。
从组中删除成员 | 已从目录中的组中删除成员。
**应用程序事件** |
添加服务主体 | 已在目录中添加服务主体。
删除服务主体 | 已从目录中删除服务主体。
添加服务主体凭据 | 已将凭据添加到服务主体。
删除服务主体凭据 | 已从服务主体中删除凭据。
添加委派条目 | 已在目录中创建 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)。
设置委派条目 | 已在目录中更新 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)。
删除委派条目 | 已在目录中删除 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)。
**角色事件** |
将角色成员添加到角色 | 已将用户添加到目录角色。
从角色中删除角色成员 | 已从目录角色中删除用户。
设置公司联系信息 | 已设置公司级的联系方法首选项。这包括营销电子邮件地址，以及有关 Microsoft Online Services 的技术通知。
**B2B 事件** |
已上载批量邀请。 | 管理员已上载一个文件，其中包含要发送到合作伙伴用户的邀请。
已处理批量邀请。 | 已处理包含合作伙伴用户邀请的文件。
邀请外部用户。 | 已邀请外部用户加入目录。
兑换外部用户邀请。 | 外部用户已兑换其加入目录的邀请。
将外部用户添加到组。 | 已将目录中组的成员资格分配给外部用户。
将外部用户分配到应用程序。 | 已将应用程序直接访问权限分配给外部用户。
创建活跃租户。 | 已通过邀请兑换在 Azure AD 中创建新租户。
创建活跃用户。 | 已通过邀请兑换在 Azure AD 中为现有租户创建用户。
**目录事件** |
将合作伙伴添加到公司 | 已将合作伙伴添加到目录。
从公司中删除合作伙伴 | 已从目录中删除合作伙伴。
将域添加到公司 | 已将域添加到目录。
从公司中删除域 | 已从目录中删除域。
更新域 | 已更新目录中的域。
设置域身份验证 | 已更改公司的默认域设置。
在域中设置联合设置 | 已更新域的联合设置。
验证域 | 已验证目录中的域。
验证电子邮件验证域 | 已使用电子邮件验证来验证目录中的域。
设置公司的 DirSyncEnabled 标志 | 已设置用来为 Azure AD Sync 启用目录的属性。
设置密码策略 | 已设置用户密码的长度和字符约束。
设置公司信息 | 已更新公司级信息。有关详细信息，请参阅 [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell cmdlet。

<!--- 

List of events that still need descriptions:

Restore Application
Set String Auth Policy
Promote tenant to partner

--->

## 审核报告保留期
Azure AD 审核报告中的事件将保留 180 天。有关保留报告的详细信息，请参阅 [Azure Active Directory Report Retention Policies（Azure Active Directory 报告保留策略）](active-directory-reporting-retention.md)。

对于想要以更长的保留期存储其审核事件的客户，可以使用报告 API 定期将审核事件提取到独立的数据存储中。有关详细信息，请参阅 [Getting Started with the Reporting API（报告 API 入门）](active-directory-reporting-api-getting-started.md)。

## 每个审核事件随附的属性

属性 | 说明
------------- | --------------------------------------------------------------
日期和时间 | 审核事件发生的日期和时间
执行组件 | 执行操作的用户或服务主体
操作 | 执行的操作
目标 | 执行的操作所针对的用户或服务主体


## “更新用户”属性
“更新用户”审核事件包含有关更新了哪些用户属性的附加信息。对于每个属性，将包含以前的值和新值。

属性 | 说明
------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | 允许用户登录。
AssignedLicense | 分配给用户的所有许可证。
AssignedPlan | 向用户分配许可证后生成的服务计划。
LicenseAssignmentDetail | 有关分配给用户的许可证的详细信息。例如，如果涉及到基于组的许可，则这包括授予许可证的组。
移动电话 | 用户的手机号码。
OtherMail | 用户的备用电子邮件地址。
OtherMobile | 用户的备用手机号码。
StrongAuthenticationMethod | 用户为 Multi-Factor Authentication 配置的验证方法列表，例如语音呼叫、短信或移动应用发送的验证码。
StrongAuthenticationRequirement | 是否为此用户强制实施、启用或禁用 Multi-Factor Authentication。
StrongAuthenticationUserDetails | 用户用于 Multi-Factor Authentication 和密码重置验证的电话号码、备用电话号码和电子邮件地址。
TelephoneNumber | 用户的电话号码。

<!--
Audit records are a required control for many compliance regulations. For customers using the Azure Active Directory Audit Report to meet their compliance regulations, it is recommended that the customer submit a copy of this help topic with the copy of the customer’s exported audit report to help explain the report details. If the auditor would like to understand the compliance regulations that Azure currently meets, direct the auditor to the [Compliance page](/support/trust-center/compliance/) of the Microsoft Azure Trust Center.
-->
<!---HONumber=Mooncake_0516_2016-->