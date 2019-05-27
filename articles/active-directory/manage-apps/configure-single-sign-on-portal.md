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
ms.openlocfilehash: fa18bc637ec31a1f83b5cab090e008715c5e0c2a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825016"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>教程：通过 Azure Active Directory 为应用程序配置基于 SAML 的单一登录

本教程使用 [Azure 门户](https://portal.azure.com)通过 Azure Active Directory (Azure AD) 为应用程序配置基于 SAML 的单一登录。 当没有[特定于应用程序的教程](../saas-apps/tutorial-list.md)时，请使用本教程。 

本教程使用 Azure 门户执行以下操作：

> [!div class="checklist"]
> * 选择基于 SAML 的单一登录模式
> * 配置特定于应用程序的域和 URL
> * 配置用户属性
> * 创建 SAML 签名证书
> * 将用户分配到应用程序
> * 配置应用程序，以便进行基于 SAML 的单一登录
> * 测试 SAML 设置

## <a name="before-you-begin"></a>开始之前

1. 如果尚未将应用程序添加到你的 Azure AD 租户，请参阅[快速入门：向 Azure AD 租户添加应用程序](add-application-portal.md)。

2. 对于[配置基本 SAML 选项](#configure-basic-saml-options)中所述的信息，请询问应用程序供应商。

3. 使用非生产环境测试本教程中的步骤。 如果没有 Azure AD 非生产环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

4. 以 Azure AD 租户的云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

## <a name="select-a-single-sign-on-mode"></a>选择单一登录模式

将某个应用程序添加到 Azure AD 租户后，可为应用程序配置单一登录。

若要打开单一登录设置，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory”。 

2. 在显示的“Azure Active Directory”导航面板中的“管理”下，选择“企业应用程序”。 此时会显示 Azure AD 租户中应用程序的随机示例。 

3. 在“应用程序类型”菜单中选择“所有应用程序”，然后选择“应用”。

4. 输入要为其配置单一登录的应用程序的名称。 例如，可以输入 **GitHub-test** 以配置在[添加应用程序](add-application-portal.md)快速入门中添加的应用程序。  

     ![显示应用程序搜索栏的屏幕截图。](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. 选择要为其配置单一登录的应用程序。

6. 在“管理”部分选择“单一登录”。 

7. 选择“SAML”以配置单一登录。 此时会显示“设置 SAML 单一登录 - 预览”页。

## <a name="configure-basic-saml-options"></a>配置基本 SAML 选项

若要配置域和 URL，请执行以下操作：

1. 联系应用程序供应商，获取以下设置的正确信息：

    | 配置设置 | SP 启动 | idP 启动 | 说明 |
    |:--|:--|:--|:--|
    | 标识符(实体 ID) | 某些应用所需 | 某些应用所需 | 唯一标识正为其配置单一登录的应用程序。 Azure AD 将该标识符作为 SAML 令牌的 Audience 参数发送回应用程序。 应用程序应当对其进行验证。 该值也在应用程序提供的任何 SAML 元数据中显示为实体 ID。|
    | 回复 URL | 可选 | 必选 | 指定应用程序应在何处接收 SAML 令牌。 回复 URL 也称断言使用者服务 (ACS) URL。 |
    | 登录 URL | 必选 | 不指定 | 当用户打开此 URL 时，服务提供程序会将用户重定向到 Azure AD 进行身份验证和登录。 Azure AD 使用此 URL 从 Office 365 或 Azure AD 访问面板启动应用程序。 如果保留空白，则当用户启动应用程序时，Azure AD 将依赖于标识提供者来启动单一登录。|
    | 中继状态 | 可选 | 可选 | 指定应用程序在完成身份验证以后将用户重定向到何处。 通常，该值是应用程序的有效 URL。 但是，某些应用程序以不同的方式使用此字段。 有关详细信息，请询问应用程序供应商。
    | 注销 URL | 可选 | 可选 | 用于将 SAML 注销响应发回到应用程序。


2. 若要编辑基本 SAML 配置选项，请选择“基本 SAML 配置”部分右上角的“编辑”图标（铅笔图标）。

     ![配置证书](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. 在该页上的相应字段中，输入步骤 1 中由应用程序供应商提供的信息。

4. 在页面顶部，选择“保存”。

## <a name="configure-user-attributes-and-claims"></a>配置用户属性和声明 

可以控制当用户登录时 Azure AD 在 SAML 令牌中向应用程序发送的信息。 通过配置用户属性来控制此信息。 例如，可将 Azure AD 配置为当用户登录时向应用程序发送用户的名称、电子邮件和员工 ID。 

这些属性可能是单一登录正常工作所必需的，也可能是可选的。 有关详细信息，请参阅[特定于应用程序的教程](../saas-apps/tutorial-list.md)或询问应用程序供应商。

1. 若要编辑用户属性和声明，请选择“用户属性和声明”部分右上角的“编辑”图标（铅笔图标）。

   为“名称标识符值”设置的默认值为 *user.principalname*。 用户标识符用于唯一标识应用程序中的每个用户。 例如，如果电子邮件地址既是用户名，也是唯一标识符，请将此值设置为 *user.mail*。

2. 若要修改“名称标识符值”，请选择“名称标识符值”字段旁边的“编辑”图标（铅笔图标）。 根据需要对标识符格式和源进行相应的更改。 完成后保存更改。 有关自定义声明的详细信息，请参阅[自定义在 SAML 令牌中为企业应用程序颁发的声明](../develop/active-directory-saml-claims-customization.md)操作指南文章。

3. 若要添加声明，请选择页面顶部的“添加新声明”。 输入**名称**，然后选择相应的源。 如果选择“属性”源，则需要选择所要使用的“源属性”。 如果选择“转换”源，则需要选择所要使用的“转换”和“参数 1”。

4. 选择“保存”。 新声明将显示在表中。
 
## <a name="generate-a-saml-signing-certificate"></a>生成 SAML 签名证书

Azure AD 使用证书对发送给应用程序的 SAML 令牌签名。 

1. 若要生成新证书，请选择“SAML 签名证书”部分右上角的“编辑”图标（铅笔图标）。

2. 在“SAML 签名证书”部分，选择“新建证书”。

3. 在显示的新证书行中，设置“过期日期”。 有关可用配置选项的详细信息，请参阅[高级证书签名选项](certificate-signing-options.md)一文。

4. 在“SAML 签名证书”部分的顶部，选择“保存”。 

## <a name="assign-users-to-the-application"></a>将用户分配到应用程序

在向组织推出应用程序之前，最好是先使用多个用户或组来测试单一登录。

> [!NOTE]
>
> 这些步骤会将你转到门户中的“用户和组”配置部分。 完成后，需要导航回到“单一登录”部分以完成本教程。

若要将用户或组分配给应用程序，请执行以下操作：

1. 在门户中打开应用程序（如果尚未打开）。
2. 在应用程序的左侧导航面板中，选择“用户和组”。
3. 选择“添加用户”。
4. 在“添加分配”部分，选择“用户和组”。
5. 若要查找特定的用户，请在“选择成员或邀请外部用户”框中键入相应的用户名。 选择该用户的个人资料照片或徽标，然后选择“选择”。 
6. 在“添加分配”部分，选择“分配”。 完成后，选定的用户会显示在“用户和组”列表中。

## <a name="set-up-the-application-to-use-azure-ad"></a>将应用程序设置为使用 Azure AD

即将完成。  需要执行的最后一个步骤是将应用程序设置为使用 Azure AD 作为 SAML 标识提供者。 

1. 向下滚动到“设置 <applicationName>”部分。 在本教程中，此部分名为“设置 GitHub-test”。 
2. 复制此部分中每个行的值。 然后，将每个值粘贴到“基本 SAML 配置”部分的相应行中。 例如，复制“设置 GitHub-test”部分的“登录 URL”值，并将其粘贴到“基本 SAML 配置”部分的“登录 URL”字段中，等等。
3. 将所有值粘贴到相应的字段后，选择“保存”。

## <a name="test-single-sign-on"></a>测试单一登录

现在可以测试设置。  

1. 打开应用程序的单一登录设置。 
2. 滚动到“使用 <applicationName> 验证单一登录”部分。 在本教程中，此部分名为“设置 GitHub-test”。
3. 选择“测试”。 此时会显示测试选项。
4. 选择“以当前用户身份登录”。 通过此测试，可以先了解单一登录是否对你（管理员）有效。

如果有错误，则会显示错误消息。 完成以下步骤：

1. 将具体内容复制并粘贴到“错误如何呈现?”框中。

    ![获取解决方案指南](media/configure-single-sign-on-portal/error-guidance.png)

2. 选择“获取解决方法指导”。 此时会显示根本原因和解决方案指南。  在本示例中，用户未分配到应用程序。

3. 阅读解决方法指导，如果方法适用，请修复问题。

4. 再次运行测试，直至成功完成。

## <a name="next-steps"></a>后续步骤
在本教程中，你已经为应用程序配置了单一登录设置。 在完成配置后，你向应用程序分配了一个用户，并将应用程序配置为使用基于 SAML 的单一登录。 在所有这些工作完成以后，你验证了 SAML 登录是否可以正常使用。

完成了以下操作：
> [!div class="checklist"]
> * 选定 SAML 作为单一登录模式
> * 就域和 URL 的配置问题联系了应用程序供应商
> * 配置了用户属性
> * 创建了 SAML 签名证书
> * 手动将用户或组分配给了应用程序
> * 已将应用程序配置为使用 Azure AD 作为 SAML 标识提供者
> * 测试了基于 SAML 的单一登录

若要向组织中的更多用户推出应用程序，请使用自动用户预配。

> [!div class="nextstepaction"]
> [了解如何使用自动预配来分配用户](configure-automatic-user-provisioning-portal.md)


