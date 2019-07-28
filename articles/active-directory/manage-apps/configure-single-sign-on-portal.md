---
title: 配置单一登录 - Azure Active Directory | Microsoft Docs
description: 本教程使用 Azure 门户通过 Azure Active Directory (Azure AD) 为应用程序配置基于 SAML 的单一登录。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: c5e8ed4a78fccce4f3a5c631a99a8729114e5722
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422601"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>如何配置基于 SAML 的单一登录

将应用添加到 Azure AD 企业应用程序后，可配置单一登录设置。 本文介绍如何为非库应用配置基于 SAML 的单一登录。 

> [!NOTE]
> 想要添加库应用？ 请在 [SaaS 应用教程列表](../saas-apps/tutorial-list.md)中找到有关设置的分步说明

若要为非库应用程序配置单一登录*而无需编写代码*，需要拥有订阅或 Azure AD Premium，并且该应用程序必须支持 SAML 2.0。 有关 Azure AD 版本的详细信息，请访问 [Azure AD 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="before-you-begin"></a>开始之前

- 如果尚未将应用程序添加到 Azure AD 租户，请参阅[添加库应用](add-gallery-app.md)或[添加非库应用](add-non-gallery-app.md)。
- 联系应用程序供应商获取以下设置的正确信息：

    | 基本 SAML 配置设置 | SP 启动 | idP 启动 | 说明 |
    |:--|:--|:--|:--|
    | 标识符(实体 ID) | 某些应用所需 | 某些应用所需 | 唯一标识正为其配置单一登录的应用程序。 Azure AD 将该标识符作为 SAML 令牌的 Audience 参数发送回应用程序。 应用程序应当对其进行验证。 该值也在应用程序提供的任何 SAML 元数据中显示为实体 ID。 可以在应用程序发送的 **AuthnRequest**（SAML 请求）中找到此值（**Issuer** 元素）。  |
    | 回复 URL | 可选 | 必选 | 指定应用程序应在何处接收 SAML 令牌。 回复 URL 也称断言使用者服务 (ACS) URL。 |
    | 登录 URL | 必选 | 不指定 | 当用户打开此 URL 时，服务提供程序会将用户重定向到 Azure AD 进行身份验证和登录。 Azure AD 使用此 URL 从 Office 365 或 Azure AD 访问面板启动应用程序。 如果保留空白，则当用户启动应用程序时，Azure AD 将依赖于标识提供者来启动单一登录。|
    | 中继状态 | 可选 | 可选 | 指定应用程序在完成身份验证以后将用户重定向到何处。 通常，该值是应用程序的有效 URL。 但是，某些应用程序以不同的方式使用此字段。 有关详细信息，请询问应用程序供应商。
    | 注销 URL | 可选 | 可选 | 用于将 SAML 注销响应发回到应用程序。

## <a name="step-1-edit-the-basic-saml-configuration"></a>步骤 1。 编辑基本 SAML 配置

1. 以 Azure AD 租户的云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

1. 导航到“Azure Active Directory” > “企业应用程序”，从列表中选择该应用程序。   
   
   - 若要搜索应用程序，请在“应用程序类型”菜单中选择“所有应用程序”，然后选择“应用”。    在“搜索”框中输入应用程序的名称，然后从结果中选择该应用程序。

1. 在“管理”部分选择“单一登录”。   

1. 选择“SAML”。  此时会显示“设置 SAML 单一登录 - 预览”页。 

1. 若要编辑基本 SAML 配置选项，请选择“基本 SAML 配置”部分右上角的“编辑”图标（铅笔图标）。  

     ![配置证书](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. 在相应的字段中，输入[开始之前](#before-you-begin)部分所述的信息。

1. 在页面顶部，选择“保存”  。

## <a name="step-2-configure-user-attributes-and-claims"></a>步骤 2. 配置用户属性和声明 

当用户登录时，应用程序可能需要使用它从 Azure AD 收到的 SAML 令牌中的特定用户属性或声明。 例如，可能需要使用特定的声明 URI 或声明值，或者，“名称”可能需要不同于在 Microsoft 标识平台中存储的用户名。  [特定于应用程序的教程](../saas-apps/tutorial-list.md)中描述了库应用的要求，你也可以咨询应用程序供应商。 下面描述了用于配置用户属性和声明的一般步骤。

1. 在“用户属性和声明”部分，选择右上角的“编辑”图标（铅笔图标）。  

1. 检查“名称标识符值”。  默认值为 *user.principalname*。 用户标识符用于唯一标识应用程序中的每个用户。 例如，如果电子邮件地址既是用户名，也是唯一标识符，请将此值设置为 *user.mail*。

1. 若要修改“名称标识符值”，请选择“名称标识符值”字段旁边的“编辑”图标（铅笔图标）。    根据需要对标识符格式和源进行相应的更改。 有关详细信息，请参阅[编辑 NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid)。 完成后保存更改。 
 
1. 若要配置组声明，请选择“声明中返回的组”字段对应的“编辑”图标。   有关详细信息，请参阅[配置组声明](../hybrid/how-to-connect-fed-group-claims.md)。

3. 若要添加声明，请选择页面顶部的“添加新声明”。  输入**名称**，然后选择相应的源。 如果选择“属性”源，则需要选择所要使用的“源属性”。   如果选择“转换”源，则需要选择所要使用的“转换”和“参数 1”。    有关详细信息，请参阅[添加特定于应用程序的声明](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims)。 完成后保存更改。 

4. 选择“保存”。  新声明将显示在表中。

   > [!NOTE]
   > 有关在应用程序中自定义 Azure AD 提供的 SAML 令牌的其他方式，请参阅以下资源。
   >- 若要通过 Azure 门户创建自定义角色，请参阅[配置角色声明](../develop/active-directory-enterprise-app-role-management.md)。
   >- 若要通过 PowerShell 自定义声明，请参阅[自定义声明 - PowerShell](../develop/active-directory-claims-mapping.md)。
   >- 若要修改应用程序清单以配置应用程序的可选声明，请参阅[配置可选声明](../develop/active-directory-optional-claims.md)。
   >- 若要为刷新令牌、访问令牌、会话令牌和 ID 令牌设置令牌生存期策略，请参阅[配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)。 或者，若要通过 Azure AD 条件访问限制身份验证会话，请参阅[身份验证会话管理功能](https://go.microsoft.com/fwlink/?linkid=2083106)。

## <a name="step-3-manage-the-saml-signing-certificate"></a>步骤 3. 管理 SAML 签名证书

Azure AD 使用证书对它发送到应用程序的 SAML 令牌进行签名。 在“设置 SAML 单一登录”页上，可以查看或下载活动的证书。  还可以更新、创建或导入证书。 对于库应用程序，应用程序的 SAML 文档中提供了有关证书格式的详细信息（请参阅[特定于应用程序的教程](../saas-apps/tutorial-list.md)）。 

1. 转到“SAML 签名证书”部分。  根据应用程序的类型，你将看到用于下载 Base64 格式、原始格式或联合元数据 XML 格式的证书的选项。 Azure AD 还提供“应用联合元数据 URL”，从中可以访问特定于应用程序的、采用 `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 格式的元数据。 

1. 若要管理、创建或导入证书，请在“SAML 签名证书”部分的右上角选择“编辑”图标（铅笔图标），然后执行下列任一操作：  

   - 若要创建新证书，请依次选择“新建证书”、“过期日期”、“保存”。    若要激活证书，请选择上下文菜单 ( **...** )，然后选择“激活证书”。 
   - 若要上传包含私钥和 pfx 凭据的证书，请选择“导入证书”并浏览到该证书。  输入 **PFX 密码**，然后选择“添加”。   
   - 若要配置高级证书签名选项，请使用以下选项。 有关这些选项的说明，请参阅[高级证书签名选项](certificate-signing-options.md)一文。
      - 在“签名选项”下拉列表中，选择“为 SAML 响应签名”、“为 SAML 断言签名”或“为 SAML 响应和断言签名”。    
      - 在“签名算法”下拉列表中，选择“SHA-1”或“SHA-256”。   
   - 若要在活动的证书即将过期时通知其他人，请在“通知电子邮件地址”字段中输入电子邮件地址。 

1. 在“SAML 签名证书”部分的顶部，选择“保存”。   

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>步骤 4. 将应用程序设置为使用 Azure AD

“设置 \<应用程序名称>”部分列出了需要在应用程序中配置的值，以便应用程序将 Azure AD 用作 SAML 标识提供者。  所需值根据应用程序的不同而异。 有关详细信息，请参阅应用程序的 SAML 文档。

1. 向下滚动到“设置 \<applicationName>”  部分。 
2. 根据需要复制此部分显示的每行中的值，并遵照有关将该值添加到应用程序的特定于应用程序的说明操作。 对于库应用，可以选择“查看分步说明”来查看文档。  
   - “登录 URL”和“注销 URL”值都解析为同一终结点，即 Azure AD 实例的 SAML 请求处理终结点。   
   - “Azure AD 标识符”是颁发给应用程序的 SAML 令牌中的 **Issuer** 值。 
1. 将所有值粘贴到相应的字段后，选择“保存”。 

## <a name="step-5-validate-single-sign-on"></a>步骤 5。 验证单一登录

现在可以测试设置，以确定你（管理员）是否可以正常使用单一登录。  

1. 打开应用程序的单一登录设置。 
2. 滚动到“使用 <applicationName> 验证单一登录”部分。  在本教程中，此部分名为“设置 GitHub-test”。 
3. 选择“测试”。  此时会显示测试选项。
4. 选择“以当前用户身份登录”。  

如果登录成功，则你可以将用户和组分配到 SAML 应用程序。
如果出现了错误消息，请完成以下步骤：

1. 将具体内容复制并粘贴到“错误如何呈现?”框中。 

    ![使用“错误如何呈现”框来获得解决方法指导](media/configure-single-sign-on-portal/error-guidance.png)

1. 选择“获取解决方法指导”。  此时会显示根本原因和解决方案指南。  在本示例中，用户未分配到应用程序。
1. 阅读解决方法指导，如果方法适用，请修复问题。
1. 再次运行测试，直至成功完成。

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](methods-for-assigning-users-and-groups.md)
- [配置用户帐户自动预配](configure-automatic-user-provisioning-portal.md)
