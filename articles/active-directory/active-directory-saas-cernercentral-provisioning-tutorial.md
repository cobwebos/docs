---
title: "教程：使用 Azure Active Directory 为 Cerner Central 配置自动用户预配 | Microsoft Docs"
description: "了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Cerner Central 和取消其预配。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: asmalser-msft
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 6ffab1ae12d257510b410a782a724d8d1b8b9272
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017

---

# <a name="tutorial-configuring-cerner-central-for-automatic-user-provisioning"></a>教程：为 Cerner Central 配置自动用户预配


本教程旨在介绍为了从 Azure AD 自动将用户帐户预配到 Cerner Central 以及取消其预配而需要在 Cerner Central 和 Azure AD 中执行的步骤。 

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

*   一个 Azure Active Directory 租户
*   Cerner Central 租户 
*   Cerner Central 中的管理员帐户 

> [!NOTE]
> Azure Active Directory 使用 [SCIM](http://www.simplecloud.info/) 协议与 Cerner Central 集成。

## <a name="assigning-users-to-cerner-central"></a>将用户分配到 Cerner Central

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只有已“分配”到 Azure AD 中的应用程序的用户和组才进行同步。 

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要 Cerner Central 访问权限的用户。 确定后，可以按照此处的说明将这些用户分配到 Cerner Central：

[向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>将用户分配到 Cerner Central 的重要提示

*    建议将单个 Azure AD 用户分配到 Cerner Central 来测试预配配置。 其他用户和/或组可以稍后分配。

*    将用户分配到 Cerner Central 时，必须在分配对话框中选择“用户”角色。 “默认访问权限”角色不可用于预配。


## <a name="configuring-user-provisioning-to-cerner-central"></a>向 Cerner Central 配置用户预配

本部分将指导用户完成以下操作：将 Azure AD 连接到 Cerner Central 的 SCIM 用户帐户预配 API，配置预配服务以便基于 Azure AD 中的用户和组分配在 Cerner Central 中创建、更新和禁用所分配的用户帐户。

> [!TIP]
> 还可以选择按照 [Azure 门户 (https://portal.azure.com) 中提供的说明为 Cerner Central 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>若要在 Azure AD 中为 Cerner Central 配置自动用户帐户预配，请执行以下操作：


为了将用户帐户预配到 Cerner Central，需要创建一个系统帐户，并生成一个 Azure AD 可用来连接 Cerner 的 SCIM 终结点的 OAuth 持有者令牌。 此外，强烈建议在将集成部署到生产环境前，先在 Cerner 沙盒环境中执行集成。

1.    第一步是确保管理 Cerner 和 Azure AD 集成的人员具有 CernerCare 帐户，需要此帐户才可访问完成此说明所必需的文档。 如有必要，请使用以下 URL 在每个适用的环境中创建 CernerCare 帐户。

   * 沙盒：https://sandboxcernercare.com/accounts/create

   * 生产：https://cernercare.com/accounts/create  

2.    接下来，必须为 Azure AD 创建系统帐户。 使用下面的说明为沙盒和生产环境请求一个系统帐户。

   * 说明：https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * 沙盒：https://sandboxcernercentral.com/system-accounts/

   * 生产：https://cernercentral.com/system-accounts/

3.    接下来，为每个系统帐户生成 OAuth 持有者令牌。 为此，请根据以下说明进行操作。

   * 说明：https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * 沙盒：https://sandboxcernercentral.com/system-accounts/

   * 生产：https://cernercentral.com/system-accounts/

4. 最后，需要在 Cerner 中获取用户名单领域 ID 来完成 Azure AD 的配置。 有关如何获取的信息，请参阅：https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM。 

5. 现可以配置 Azure AD 向 Cerner 预配用户帐户。 登录到[Azure 门户](https://portal.azure.com)，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

6. 如果已为 Cerner Central 配置单一登录，请使用搜索字段搜索 Cerner Central 实例。 否则，请选择“添加”并在应用程序库中搜索“Cerner Central”。 从搜索结果中选择 Cerner Central，并将其添加到应用程序列表。

7.    选择 Cerner Central 的实例，然后选择“预配”选项卡。

8.    将“预配模式”设置为“自动”。

   ![Cerner Central 预配](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  在“管理员凭据”下填写以下字段：

   * 在“租户 URL”字段中，输入以下格式的 URL，将“User-Roster-Realm-ID”替换为在第 4 步中获取的领域 ID。

> https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/Users 

   * 在“密钥标记”字段中，输入在第 3 步中生成的 OAuth 持有者令牌并单击“测试连接”。

   * 应当会在门户的右上端看到一条成功通知。

10. 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中下面的复选框。

11. 单击“保存” 。 

12. 在“属性映射”部分中，查看将从 Azure AD 同步到 Cerner Central 的用户和组属性。 请注意，选为“匹配”属性的特性将用于匹配 Cerner Central 中的用户帐户和组以执行更新操作。 选择“保存”按钮以提交任何更改。

13. 若要为 Cerner Central 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

14. 单击“保存” 。 

这将开始对在“用户和组”部分中分配给 Cerner Central 的任何用户和/或组进行初始同步。 请注意，初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务对 Cerner Central 应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting)。

## <a name="additional-resources"></a>其他资源

* [教程：使用 Azure Active Directory 为 Cerner Central 配置单一登录](active-directory-saas-cernercentral-tutorial.md)
* [管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>后续步骤
* [了解如何查看日志并获取有关预配活动的报表](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting)。

