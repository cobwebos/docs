---
title: IDology 与 Azure Active Directory B2C 的集成
titleSuffix: Azure AD B2C
description: 了解如何在 Azure AD B2C 中将示例在线付款应用与 IDology 集成。 IDology 是具有多个解决方案的标识验证和校对提供程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d34bb14dd0f474ff9350fec513c02fbb470d6738
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385648"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>有关配置 IDology 与 Azure Active Directory B2C 的教程 

在此示例教程中，我们提供了有关如何将 Azure AD B2C 与[IDology](https://www.idology.com/solutions/)集成的指导。 IDology 是具有多个解决方案的标识验证和校对提供程序。 在此示例中，我们将通过 IDology 介绍 ExpectID 解决方案。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 链接到 Azure 订阅的[Azure AD B2C 租户](tutorial-create-tenant.md)。

## <a name="scenario-description"></a>方案描述

IDology 集成包括以下组件：

- Azure AD B2C –负责验证用户凭据的授权服务器。 它也称为标识提供者。
- IDology – IDology 服务获取用户提供的输入，并验证用户的身份。
- 自定义 Rest API –此 API 实现 Azure AD 与 IDology 服务之间的集成。

下面的体系结构关系图显示了实现。

![IDology 体系结构图](media/partner-idology/idology-architecture-diagram.png)

|      |      |
|------|------|
|1     | 用户进入登录页。 |
|2     | 用户选择注册选项以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。 |
|3     | Azure AD B2C 调用中间层 API 并传递用户特性。 |
|4     | 中间层 API 收集用户属性，并将其转换为 IDOlogy API 可以使用的格式。 然后，它会将信息发送到 IDology。 |
|5     | IDology 将使用该信息并对其进行处理，然后将结果返回到中间层 API。 |
|6     | 中间层 API 处理信息并将相关信息发送回 Azure AD B2C。 |
|7     | Azure AD B2C 接收来自中间层 API 的信息。 如果显示**失败**响应，则向用户显示一条错误消息。 如果它显示**成功**响应，则会对用户进行身份验证并将其写入到目录中。 |
|      |      |

> [!NOTE]
> Azure AD B2C 还可以要求客户执行单步身份验证，但本教程超出了本教程的范围。

## <a name="onboard-with-idology"></a>内置 IDology

1. IDology 提供多种解决方案，可在[此处](https://www.idology.com/solutions/)找到。 对于此示例，我们使用 ExpectID。

2. 若要创建 IDology 帐户，请联系[IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)。

3. 创建帐户后，你将收到 API 配置所需的信息。 以下各节介绍了该过程。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

将提供的 API 代码部署到 Azure 服务。 可以按照这些[说明](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)从 Visual Studio 发布代码。

需要部署的服务的 URL 来配置 Azure AD，并提供所需的设置。

### <a name="part-2---configure-the-api"></a>第2部分-配置 API 

可以[在 Azure 中的应用服务中配置](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)应用程序设置。 使用此方法，可以安全地配置设置，而无需将其签入存储库。 需要为 Rest API 提供以下设置：

| 应用程序设置 | 源 | 说明 |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology 帐户配置 |     |
|IdologySettings:ApiPassword | IDology 帐户配置 |     |
|WebApiSettings:ApiUsername |定义 API 的用户名| 在 ExtId 配置中使用 |
|WebApiSettings:ApiPassword | 定义 API 的密码 | 在 ExtId 配置中使用

### <a name="part-3---create-api-policy-keys"></a>第3部分-创建 API 策略密钥

按照本[文档](secure-rest-api.md#add-rest-api-username-and-password-policy-keys)创建两个策略密钥：一个用于 api 用户名，另一个用于前面定义的 api 密码。

示例策略使用这些密钥名称：

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第4部分-配置 Azure AD B2C 策略

1. 按照此[文档](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)下载[LocalAccounts starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)并为 Azure AD B2C 租户配置策略。 按照说明进行操作，直到完成 "**测试自定义策略"** 部分。

2. [在此处](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)下载两个示例策略。

3. 更新两个示例策略：

   1. 打开两个策略：

      1. 在部分中 `Idology-ExpectId-API` ， `ServiceUrl` 用上面部署的 API 的位置更新元数据项。

      1. 将 `yourtenant` 替换为 Azure AD B2C 租户的名称。
      例如，如果 Azure AD B2C 租户的名称为，则将  `contosotenant` 的所有实例替换为  `yourtenant.onmicrosoft.com`   `contosotenant.onmicrosoft.com` 。

   1. 打开文件 TrustFrameworkExtensions.xml：

      1. 查找元素  `<TechnicalProfile Id="login-NonInteractive">` 。 将的两个实例替换  `IdentityExperienceFrameworkAppId`   为前面创建的 IdentityExperienceFramework 应用程序的应用程序 ID。

      1. 将的两个实例替换  `ProxyIdentityExperienceFrameworkAppId`   为前面创建的 ProxyIdentityExperienceFramework 应用程序的应用程序 ID。

4. 在步骤1中，用两个更新的示例策略替换之前上传到 Azure AD B2C 的 SignInorSignUp.xml 和 TrustFrameworkExtensions.xml。

> [!NOTE]
> 作为最佳做法，我们建议客户在 "属性集合" 页中添加许可通知。 通知用户将信息发送到第三方服务进行身份验证。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，然后在 "**策略**" 下选择 "**用户流**"。

2. 选择以前创建的**用户流**。

3. 选择 "**运行用户流**"，然后选择设置：

   1. **应用程序**-选择注册的应用（示例为 JWT）。

   1. **回复 url** -选择 "**重定向 url**"。

   1. 选择“运行用户流”。

4. 浏览注册流并创建帐户。

5. 注销。

6. 完成登录流。

7. 输入 "**继续**" 后，将显示 IDology 拼图。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](custom-policy-get-started.md?tabs=applications) 

