---
title: SAML 单一登录-非库应用程序-Microsoft 标识平台 |Microsoft Docs
description: 配置 Microsoft 标识平台中的非库应用程序的单一登录 (SSO) (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 057fa4dc9080ea0216765d89fa6f9d54c60ccec1
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422790"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>为非库应用程序配置基于 SAML 的单一登录

[将库应用](add-gallery-app.md)或[非库 web 应用](add-non-gallery-app.md)添加到 Azure AD 企业应用程序时, 可以使用的单一登录选项之一是[基于 SAML 的单一登录](what-is-single-sign-on.md#saml-sso)。 尽可能为使用 SAML 协议进行身份验证的应用程序选择 SAML。 Azure AD 使用 SAML 单一登录通过用户的 Azure AD 帐户对应用程序进行身份验证。 Azure AD 通过连接协议将登录信息传递给应用程序。 可以根据在 SAML 声明中定义的规则将用户映射到特定应用程序角色。 本文介绍如何为非库应用程序配置基于 SAML 的单一登录。 

> [!NOTE]
> 添加库应用？ 在[SaaS 应用教程列表](../saas-apps/tutorial-list.md)中查找分步安装说明

若要在不编写代码的情况下为非库应用程序配置 SAML 单一登录, 需要具有一个订阅或 Azure AD Premium, 并且该应用程序必须支持 SAML 2.0。 有关 Azure AD 版本的详细信息，请访问 [Azure AD 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="before-you-begin"></a>开始之前

如果尚未将应用程序添加到 Azure AD 租户, 请参阅[添加非库应用](add-non-gallery-app.md)。

## <a name="step-1-edit-the-basic-saml-configuration"></a>步骤 1。 编辑基本 SAML 配置

1. 以 Azure AD 租户的云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

2. 导航到**Azure Active Directory** > "**企业应用程序**", 然后从列表中选择应用程序。 
   
   - 要搜索应用程序, 请在 "**应用程序类型**" 菜单中选择 "**所有应用程序**", 然后选择 "**应用**"。 在 "搜索" 框中输入应用程序的名称, 然后从结果中选择应用程序。

3. 在“管理”部分选择“单一登录”。 

4. 选择 " **SAML**"。 此时会显示“设置 SAML 单一登录 - 预览”页。

   ![步骤1编辑基本 SAML 配置](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. 若要编辑基本 SAML 配置选项，请选择“基本 SAML 配置”部分右上角的“编辑”图标（铅笔图标）。

1. 输入以下设置。 你应从应用程序供应商处获取值。 可以手动输入值，或上传元数据文件以提取字段值。

    | 基本 SAML 配置设置 | SP 启动 | idP 启动 | 描述 |
    |:--|:--|:--|:--|
    | **标识符 (实体 ID)** | 某些应用所需 | 某些应用所需 | 唯一标识该应用程序。 Azure AD 将该标识符作为 SAML 令牌的 Audience 参数发送回应用程序。 应用程序应当对其进行验证。 该值也在应用程序提供的任何 SAML 元数据中显示为实体 ID。 *可以在应用程序发送的**AuthnRequest** (SAML 请求) 中找到此值作为**颁发者**元素。* |
    | **回复 URL** | 可选 | 必填 | 指定应用程序应在何处接收 SAML 令牌。 回复 URL 也称断言使用者服务 (ACS) URL。 您可以使用 "其他回复 URL" 字段指定多个答复 Url。 例如, 你可能需要多个子域的其他回复 Url。 或者, 出于测试目的, 可以一次指定多个回复 Url (本地主机和公共 Url)。 |
    | **登录 URL** | 必填 | 不指定 | 当用户打开此 URL 时，服务提供程序会将用户重定向到 Azure AD 进行身份验证和登录。 Azure AD 使用此 URL 从 Office 365 或 Azure AD 访问面板启动应用程序。 如果为空, 当用户从 Office 365、Azure AD 访问面板或 Azure AD SSO URL 启动应用程序时, Azure AD 会执行 IdP 启动的登录。|
    | **中继状态** | 可选 | 可选 | 指定应用程序在完成身份验证以后将用户重定向到何处。 通常，该值是应用程序的有效 URL。 但是，某些应用程序以不同的方式使用此字段。 有关详细信息，请询问应用程序供应商。
    | **注销 URL** | 可选 | 可选 | 用于将 SAML 注销响应发回到应用程序。

有关详细信息, 请参阅[单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md)。

## <a name="step-2-configure-user-attributes-and-claims"></a>步骤 2. 配置用户属性和声明 

当用户对应用程序进行身份验证时, Azure AD 向应用程序颁发 SAML 令牌, 其中包含有关唯一标识它们的用户的信息 (或声明)。 默认情况下, 此信息包括用户的用户名、电子邮件地址、名字和姓氏。 例如, 如果应用程序需要特定声明值或用户名之外的**名称**格式, 则可能需要自定义这些声明。 [应用程序特定教程](../saas-apps/tutorial-list.md)中介绍了库应用的要求, 或者你可以询问应用程序供应商。 下面介绍了配置用户属性和声明的一般步骤。

1. 在 "**用户属性和声明**" 部分中, 选择右上角的 "**编辑**" 图标 (铅笔)。

   ![步骤2配置用户属性和声明](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. 验证**名称标识符值**。 默认值为*principalname*。 用户标识符用于唯一标识应用程序中的每个用户。 例如，如果电子邮件地址既是用户名，也是唯一标识符，请将此值设置为 *user.mail*。

3. 若要修改“名称标识符值”，请选择“名称标识符值”字段旁边的“编辑”图标（铅笔图标）。 根据需要对标识符格式和源进行相应的更改。 有关详细信息, 请参阅[编辑 NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid)。 完成后保存更改。 
 
4. 若要配置组声明, 请选择 "声明" 字段**中返回的组**的**编辑**图标。 有关详细信息, 请参阅[配置组声明](../hybrid/how-to-connect-fed-group-claims.md)。

5. 若要添加声明，请选择页面顶部的“添加新声明”。 输入**名称**，然后选择相应的源。 如果选择“属性”源，则需要选择所要使用的“源属性”。 如果选择“转换”源，则需要选择所要使用的“转换”和“参数 1”。 有关详细信息, 请参阅[添加特定于应用程序的声明](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims)。 完成后保存更改。 

6. 选择**保存**。 新声明将显示在表中。

   > [!NOTE]
   > 有关将 SAML 令牌从 Azure AD 自定义到应用程序的其他方式, 请参阅以下资源。
   >- 若要通过 Azure 门户创建自定义角色, 请参阅[配置角色声明](../develop/active-directory-enterprise-app-role-management.md)。
   >- 若要通过 PowerShell 自定义声明, 请参阅[自定义声明-PowerShell](../develop/active-directory-claims-mapping.md)。
   >- 若要修改应用程序清单以配置应用程序的可选声明, 请参阅[配置可选声明](../develop/active-directory-optional-claims.md)。
   >- 若要为刷新令牌、访问令牌、会话令牌和 ID 令牌设置令牌生存期策略, 请参阅[配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)。 或者, 若要通过 Azure AD 条件访问限制身份验证会话, 请参阅[身份验证会话管理功能](https://go.microsoft.com/fwlink/?linkid=2083106)。

## <a name="step-3-manage-the-saml-signing-certificate"></a>步骤 3. 管理 SAML 签名证书

Azure AD 使用证书对它发送到应用程序的 SAML 令牌进行签名。 需要使用此证书在 Azure AD 与应用程序之间设置信任。 有关证书格式的详细信息，请参阅应用程序的 SAML 文档。 有关详细信息, 请参阅[在 SAML 令牌中](certificate-signing-options.md)[管理用于联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)和高级证书签名选项。

从 Azure AD 中, 可以直接从主**设置 SAML 的单一登录**页下载 Base64 或 Raw 格式的活动证书。 或者, 你可以通过下载应用程序元数据 XML 文件或使用应用联合元数据 URL 来获取活动证书。 若要查看、创建或下载证书 ("活动" 或 "非活动"), 请执行以下步骤。

1. 请参阅 " **SAML 签名证书**" 部分。 

   ![步骤3管理 SAML 签名证书](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. 验证证书是否具有：

   - *所需的到期日期。* 你可以将到期日期配置为在未来最多三年。
   - *所需证书的活动状态。* 如果状态为 "**非**活动", 则将状态更改为 "**活动**"。 若要更改状态, 请右键单击所需证书的行, 然后选择 "**使证书处于活动**状态"。
   - *正确的签名选项和算法。*
   - *正确的通知电子邮件地址。* 当活动证书接近到期日期时, Azure AD 向此字段中配置的电子邮件地址发送通知。

2. 若要下载证书, 请选择 Base64 格式、原始格式或联合元数据 XML 的选项之一。 Azure AD 还提供**应用联合元数据 Url** , 你可以在其中以格式`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`访问特定于应用程序的元数据。

3. 若要管理、创建或导入证书, 请选择 " **SAML 签名证书**" 部分右上角的 "**编辑**" 图标 (铅笔)。

   ![SAML 签名证书](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   执行以下任一操作:

   - 若要创建新证书, 请选择 "**新建证书**", 选择**过期日期**, 然后选择 "**保存**"。 若要激活证书, 请选择上下文菜单 ( **...** ) 并选择 "**使证书处于活动状态**"。
   - 若要上传具有私钥和 pfx 凭据的证书, 请选择 "**导入证书**" 并浏览到该证书。 输入**PFX 密码**, 然后选择 "**添加**"。  
   - 若要配置高级证书签名选项, 请使用以下选项。 有关这些选项的说明, 请参阅[高级证书签名选项](certificate-signing-options.md)一文。
      - 在 "**签名选项**" 下拉列表中, 选择 "**签名 saml 响应**"、"**签名 Saml 断言**" 或 "**签名 saml 响应和断言**"。
      - 在 "**签名算法**" 下拉列表中, 选择 " **sha-1** " 或 " **sha-256**"。
   - 若要在活动证书接近其到期日期时通知其他人, 请在 "**通知电子邮件地址**" 字段中输入电子邮件地址。

4. 如果进行了更改, 请选择 " **SAML 签名证书**" 部分顶部的 "**保存**"。 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>步骤 4. 将应用程序设置为使用 Azure AD

"**设置\<applicationName >** " 部分列出了需要在应用程序中配置的值, 以便它将 Azure AD 作为 SAML 标识提供者使用。 所需值根据应用程序的不同而异。 有关详细信息，请参阅应用程序的 SAML 文档。 若要查找文档, 请参阅**设置\<应用程序名称 >** 标题, 并选择 "**查看分步说明**"。 文档将出现在 "**配置登录**" 页中。 该页指导您在 "**设置\<应用程序名称" >** 标题中填写**登录 url**、 **Azure AD 标识符**和**注销 URL**值。

1. 向下滚动到“设置 \<applicationName>”部分。 
   
   ![步骤4设置应用程序](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. 根据需要, 从此部分的每行复制值, 并按照应用程序特定的说明将该值添加到应用程序。 对于库应用程序, 可以通过选择 "**查看分步说明**" 来查看文档。 
   - "**登录 url** " 和 "**注销 URL** " 值都解析为同一终结点, 该终结点是 Azure AD 实例的 SAML 请求处理终结点。 
   - **Azure AD 标识符**是颁发给应用程序的 SAML 令牌中的**颁发者**的值。
2. 将所有值粘贴到相应的字段后，选择“保存”。

## <a name="step-5-validate-single-sign-on"></a>步骤 5。 验证单一登录

将应用程序配置为使用 Azure AD 作为基于 SAML 的标识提供程序后, 你可以测试设置以查看单一登录是否适用于你的帐户。 

2. 滚动到“使用 <applicationName> 验证单一登录”部分。

   ![步骤5验证单一登录](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. 选择**验证**。 此时会显示测试选项。

4. 选择“以当前用户身份登录”。 

如果登录成功, 你可以将用户和组分配给 SAML 应用程序。
如果出现错误消息, 请完成以下步骤:

1. 将具体内容复制并粘贴到“错误如何呈现?”框中。

    ![获取解决方案指南](media/configure-single-sign-on-portal/error-guidance.png)

2. 选择“获取解决方法指导”。 此时会显示根本原因和解决方案指南。  在本示例中，用户未分配到应用程序。

3. 阅读解决方法指导，如果方法适用，请修复问题。

4. 再次运行测试，直至成功完成。

有关详细信息, 请参阅[在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录](../develop/howto-v1-debug-saml-sso-issues.md)。

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](methods-for-assigning-users-and-groups.md)
- [配置自动用户帐户预配](configure-automatic-user-provisioning-portal.md)