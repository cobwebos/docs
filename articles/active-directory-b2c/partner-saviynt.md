---
title: 有关配置 Saviynt 与 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何 Azure Active Directory B2C 配置 Saviynt 的以实现跨应用程序集成，从而精简 IT 现代化，提高安全性、治理和合规性。 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8406074933489e53e9235a8a6a05b68f1dd42a85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259130"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>有关配置 Saviynt 与 Azure Active Directory B2C 的教程

在此示例教程中，我们提供了有关如何将 Azure Active Directory (AD) B2C 与 [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/)集成的指导。 Saviynt 的安全管理器平台在一个统一的平台中提供当今企业所需的可见性、安全性和监管。 Saviynt 包含应用程序风险和监管、基础结构管理、特权帐户管理和客户风险分析。

在此示例教程中，你将设置 Saviynt，以便为 Azure AD B2C 用户提供基于委派管理的精细访问控制。 Saviynt 执行以下检查来确定用户是否有权管理 Azure AD B2C 的用户。

- 功能级别的安全性，以确定用户是否可以执行特定操作。 例如，创建用户、更新用户、重置用户密码等。

- 字段级别安全性：用于确定用户是否可以在执行用户管理操作期间读取/写入另一个用户的特定属性。 例如，技术支持代理只能更新电话号码，所有其他属性为只读。

- 用于确定用户是否可以对特定用户执行特定操作的数据级别安全性。 例如，UK 地区的技术支持管理员只能管理英国用户。

## <a name="prerequisites"></a>必备条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。 租户已链接到 Azure 订阅。

- Saviynt [订阅](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>方案描述

Saviynt 集成包括以下组件：

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) –作为一项服务的企业到客户身份，可对客户注册、登录和管理其配置文件的方式进行自定义控制。

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) –标识调控平台，为用户的生命周期管理和 Azure AD B2C 访问管理提供精细的委派管理。  

- [MICROSOFT GRAPH api](https://docs.microsoft.com/graph/use-the-api) –此 api 提供用于 Saviynt 的接口，用于管理 Azure AD B2C 用户及其在 Azure AD B2C 中的访问权限。

下面的体系结构关系图显示了实现。

![显示 saviynt 体系结构关系图的图像](./media/partner-saviynt/saviynt-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 委派的管理员通过 Saviynt 启动管理 Azure AD B2C 用户操作。
| 2. | 如果委派的管理员可以执行特定操作，Saviynt 将通过其授权引擎进行验证。
| 3. | Saviynt 的授权引擎发送授权成功/失败响应。
| 4. | Saviynt 允许委派的管理员执行所需的操作。
| 5. | Saviynt 调用 Microsoft Graph API 以及用于管理用户的用户属性 Azure AD B2C
| 6. | Microsoft Graph API 将依次在 Azure AD B2C 中创建/更新/删除用户。
| 7. | Azure AD B2C 将发送成功/失败响应。
| 8. | Microsoft Graph API 然后将响应返回到 Saviynt。

## <a name="onboard-with-saviynt"></a>内置 Saviynt

1. 若要创建 Saviynt 帐户，请联系 [Saviynt](https://saviynt.com/contact-us/)

2. 创建委派的管理策略，并为用户分配各种角色的 [委派管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-concept-delegation) 。

## <a name="configure-azure-ad-b2c-with-saviynt"></a>配置 Saviynt Azure AD B2C

### <a name="create-an-azure-ad-application-for-saviynt"></a>为 Saviynt 创建 Azure AD 应用程序

1. 登录 [Azure 门户](https://portal.azure.com/#home)。

2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。

3. 在 Azure 门户中，搜索并选择 " **Azure AD B2C**"。

4. 选择**应用注册**"  >  **新注册**"。

5. 输入应用程序的名称。 例如，Saviynt，然后选择 " **创建**"。

6. 中转到 " **API 权限** " 并选择 " **+ 添加权限"。**

7. 会显示“请求获取 API 权限”页。 选择 " **Microsoft api** " 选项卡，并选择 " **Microsoft Graph** " 作为常用 Microsoft api。

8. 前往下一页，选择 " **应用程序权限**"。

9. 选择 "**目录**"，然后选择 "**目录**" 和 "目录"。**所有复选框。**

10. 选择 " **添加权限**"。 查看添加的权限。

11. 选择 "**为默认目录保存授予管理员许可**"  >  **Save**。

12. 中转到 " **证书和机密** " 并选择 " **+ 添加客户端密码**"。 输入客户端机密说明，选择 "过期" 选项，然后选择 " **添加**"。

13. 密钥是生成的，并显示在 "客户端密钥" 部分中。

    >[!NOTE]
    > 稍后你将需要客户端密钥。

14. 转到 " **概述** "，获取 " **客户端 id** " 和 " **租户 id**"。

15. 需要租户 ID、客户端 ID 和客户端机密才能完成 Saviynt 中的设置。

### <a name="enable-saviynt-to-delete-users"></a>启用 Saviynt 以删除用户

以下步骤说明了如何启用 Saviynt 以 Azure AD B2C 中执行用户删除操作。

>[!NOTE]
>[在授予管理角色对服务主体的访问权限之前评估风险。](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

1. 在 Windows 工作站/服务器上安装最新版本的 MSOnline PowerShell 模块。

2. 连接到 AzureAD PowerShell 模块并执行以下命令：

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>测试解决方案

浏览到 Saviynt 应用程序租户并测试用户生命周期管理和访问控制用例。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

- [创建 web API 应用程序](https://docs.microsoft.com/azure/active-directory-b2c/add-web-api-application)
