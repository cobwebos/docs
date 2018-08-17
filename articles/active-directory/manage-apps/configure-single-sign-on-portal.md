---
title: 配置单一登录 - Azure Active Directory | Microsoft Docs
description: 本教程使用 Azure 门户通过 Azure Active Directory (Azure AD) 为应用程序配置基于 SAML 的单一登录。
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 08/09/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b0180f162996c5fc4647071feaf02d42320b7c9a
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036267"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>教程：通过 Azure Active Directory 为应用程序配置基于 SAML 的单一登录

本教程使用 [Azure 门户](https://portal.azure.com)通过 Azure Active Directory (Azure AD) 为应用程序配置基于 SAML 的单一登录。 请将本教程用于配置没有[特定于应用程序的教程](../saas-apps/tutorial-list.md)的应用程序。 


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

1. 如果尚未将应用程序添加到 Azure AD 租户，请参阅[快速入门：向 Azure AD 租户添加应用程序](add-application-portal.md)。

2. 有关[配置域和 URL](#configure-domain-and-urls) 中所述信息，请询问应用程序供应商。

3. 若要测试本教程中的步骤，建议使用非生产环境。 如果没有 Azure AD 非生产环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

4. 以 Azure AD 租户全局管理员、云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

## <a name="select-a-single-sign-on-mode"></a>选择单一登录模式

将某个应用程序添加到 Azure AD 租户以后，即可为应用程序配置单一登录。

若要打开单一登录设置，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。 

2. 在“Azure Active Directory”边栏选项卡中，单击“企业应用程序”。 此时“所有应用程序”边栏选项卡会打开，显示 Azure AD 租户中应用程序的随机示例。 

3. 在“应用程序类型”菜单中，选择“所有应用程序”，然后单击“应用”。

4. 输入要为其配置单一登录的应用程序的名称。 选择自己的应用程序，或使用在[添加应用程序](add-application-portal.md)快速入门中添加的 GitHub-test 应用程序。

5. 单击“单一登录”。 在“单一登录模式”下，“基于 SAML 的登录”会显示为默认选项。 

    ![配置选项](media/configure-single-sign-on-portal/config-options.png)

6. 单击边栏选项卡顶部的“保存”。 

## <a name="configure-domain-and-urls"></a>配置域和 URL

若要配置域和 URL，请执行以下操作：

1. 联系应用程序供应商，获取以下设置的正确信息：

    | 配置设置 | SP 启动 | idP 启动 | Description |
    |:--|:--|:--|:--|
    | 登录 URL | 必选 | 请勿指定 | 当用户打开此 URL 时，服务提供程序会将用户重定向到 Azure AD 进行身份验证和登录。 Azure AD 使用此 URL 从 Office 365 和 Azure AD 访问面板启动应用程序。 如果此项为空白，则当用户从 Office 365、Azure AD 访问面板或 Azure AD 单一登录 URL 启动应用程序时，Azure AD 会执行 idP 启动的单一登录。|
    | 标识符(实体 ID) | 某些应用所需 | 某些应用所需 | 唯一标识正为其配置单一登录的应用程序。 Azure AD 将标识符发送回应用程序，作为 SAML 令牌的 Audience 参数，预计由应用程序对其进行验证。 该值也在应用程序提供的任何 SAML 元数据中显示为实体 ID。|
    | 回复 URL | 可选 | 必选 | 指定应用程序应在何处接收 SAML 令牌。 回复 URL 也称断言使用者服务 (ACS) URL。 |
    | 中继状态 | 可选 | 可选 | 指定应用程序在完成身份验证以后将用户重定向到何处。 该值通常是应用程序的有效 URL，但某些应用程序会将此字段用于其他用途。 有关详细信息，请询问应用程序供应商。

2. 输入信息。 若要查看所有设置，请单击“显示高级 URL 设置”。

    ![配置选项](media/configure-single-sign-on-portal/config-urls.png)

3. 在边栏选项卡顶部单击“保存”。

4. 此部分有一个“测试 SAML 设置”按钮。 请在本教程后面的[测试单一登录](#test-single-sign-on)部分运行此测试。

## <a name="configure-user-attributes"></a>配置用户属性

可以通过用户属性控制 Azure AD 发送给应用程序的具体信息。 例如，Azure AD 可以将用户的名称、电子邮件和雇员 ID 发送给应用程序。 每次用户登录时，Azure AD 就会在 SAML 令牌中将用户属性发送给应用程序。 

这些属性可能是单一登录正常工作所必需的，也可能是可选的。 有关详细信息，请参阅[特定于应用程序的教程](../saas-apps/tutorial-list.md)或询问应用程序供应商。

1. 若要查看所有选项，请单击“查看和编辑所有其他的用户属性”。

    ![配置用户属性](media/configure-single-sign-on-portal/config-user-attributes.png)

2. 输入**用户标识符**。

    用户标识符用于唯一标识应用程序中的每个用户。 例如，如果电子邮件地址既是用户名，也是唯一标识符，请将此值设置为 *user.mail*。

3. 若要了解更多的 SAML 令牌属性，请单击“查看和编辑所有其他的用户属性”。

4. 若要将属性添加到“SAML 令牌属性”，请单击“添加属性”。 输入**名称**，然后从菜单中选择“值”。

5. 单击“ **保存**”。 然后就能在表中看到新属性了。
 
## <a name="create-a-saml-signing-certificate"></a>创建 SAML 签名证书

Azure AD 使用证书对发送给应用程序的 SAML 令牌签名。 

1. 若要查看所有选项，请单击“显示高级证书签名选项”。

    ![配置证书](media/configure-single-sign-on-portal/config-certificate.png)

2. 若要配置证书，请单击“创建新证书”。

3. 在“创建新证书”边栏选项卡中设置到期日期，然后单击“保存”。

4. 单击“使新证书处于活动状态”。

5. 若要了解详细信息，请参阅[高级证书签名选项](certificate-signing-options.md)。

6. 若要保留目前所做的更改，请确保单击“单一登录”边栏选项卡顶部的“保存”。 

## <a name="assign-users-to-the-application"></a>将用户分配到应用程序

Microsoft 建议在向组织推出应用程序之前，先使用多个用户或组对单一登录进行测试。

若要将用户或组分配给应用程序，请执行以下操作：

1. 在门户中打开应用程序（如果尚未打开）。
2. 在左侧应用程序边栏选项卡中，单击“用户和组”。
3. 单击“添加用户”。
4. 在“添加分配”边栏选项卡中，单击“用户和组”。
5. 若要查找特定用户，请在“选择”框中键入用户名，单击用户个人资料照片或徽标旁边的复选框，然后单击“选择”。 
6. 找到当前的用户名并将其选中。 你可以选择多个用户。
7. 在“添加分配”边栏选项卡中，单击“分配”。 完成后，所选用户会显示在“用户和组”列表中。

## <a name="configure-the-application-to-use-azure-ad"></a>配置应用程序以使用 Azure AD

即将完成。  最后一步需配置应用程序，以便将 Azure AD 用作 SAML 标识提供者。 

1. 向下滚动到应用程序的“单一登录”边栏选项卡的末尾。 

    ![配置应用程序](media/configure-single-sign-on-portal/configure-app.png)

2. 在门户中单击“配置应用程序”，并按说明操作。
3. 在应用程序中手动创建用户帐户，以便测试单一登录。 请创建在[上一部分](#assign-users-to-the-application)分配给应用程序的用户帐户。   做好向组织推出应用程序的准备后，建议使用自动用户预配在应用程序中自动创建用户帐户。

## <a name="test-single-sign-on"></a>测试单一登录

可以测试设置了。  

1. 打开应用程序的单一登录设置。 
2. 滚动到“配置域和 URL”部分。
2. 单击“测试 SAML 设置”。 此时会显示测试选项。

    ![测试单一登录选项](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. 单击“作为当前用户登录”。 这样可以先了解单一登录是否对你（管理员）有效。
4. 如果有错误，则会显示错误消息。 将具体内容复制并粘贴到“错误如何呈现?”框中。

    ![获取解决方案指南](media/configure-single-sign-on-portal/error-guidance.png)

5. 单击“获取解决方案指南”。 此时会显示根本原因和解决方案指南。  在本示例中，用户未分配到应用程序。

    ![修复错误](media/configure-single-sign-on-portal/fix-error.png)

6. 阅读解决方案指南，然后单击“修复它”（如果适用）。

7. 再次运行测试，直至成功完成。



## <a name="next-steps"></a>后续步骤
在本教程中，你通过 Azure 门户配置了应用程序，以便使用 Azure AD 进行单一登录。 你找到了单一登录配置页，并配置了单一登录设置。 在完成配置后，你向应用程序分配了一个用户，并将应用程序配置为使用基于 SAML 的单一登录。 在所有这些工作完成以后，你验证了 SAML 登录是否可以正常使用。

完成了以下操作：
> [!div class="checklist"]
> * 选定 SAML 作为单一登录模式
> * 就域和 URL 的配置问题联系了应用程序供应商
> * 配置了用户属性
> * 创建了 SAML 签名证书
> * 手动将用户或组分配给了应用程序
> * 配置了应用程序，以便进行单一登录
> * 测试了基于 SAML 的单一登录

若要向组织中的更多用户推出应用程序，建议使用自动预配。

> [!div class="nextstepaction"]
>[了解如何使用自动预配来分配用户](configure-automatic-user-provisioning-portal.md)


