---
title: 通过 LexisNexis 配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与 LexisNexis （这是一种分析和身份验证服务）相集成，并使用它来验证用户标识，并根据用户设备提供全面的风险评估。
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c753e9a18f9869e1bf11aa437fb60484f2553e17
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259248"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>有关配置 LexisNexis 与 Azure Active Directory B2C 的教程

在此示例教程中，我们提供了有关如何将 Azure AD B2C 与 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)集成的指导。 LexisNexis 提供多种解决方案，可在 [此处](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)找到。 在此示例教程中，我们将介绍 LexisNexis 中的 **ThreatMetrix** 解决方案。 ThreatMetrix 是一种分析和身份验证服务。 它用于验证用户标识，并根据用户设备提供全面的风险评估。

此集成根据用户信息的一些部分进行分析，这些信息由用户在注册流期间提供。 ThreatMetrix 确定是否应该允许用户继续登录。 以下属性在 ThreatMetrix 的风险分析中考虑：

- 电子邮件
- 电话号码
- 从用户的计算机收集的分析信息

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的[Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。

## <a name="scenario-description"></a>方案描述

ThreatMetrix 集成包括以下组件：

- Azure AD B2C –授权服务器，负责验证用户的凭据，也称为标识提供者

- ThreatMetrix – ThreatMetrix 服务获取用户提供的输入，并将其与从用户计算机中收集的分析信息组合在一起，以验证用户交互的安全性。

- 自定义 Rest API –此 API 实现 Azure AD B2C 与 ThreatMetrix 服务之间的集成。

下面的体系结构关系图显示了实现。

![lexisnexis 的屏幕截图](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|步骤 | 说明 |
|:--------------|:-------------|
|1. | 用户到达登录页。 用户选择 "注册" 以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用中间层 API 并传递用户特性。
| 3. | 中间层 API 收集用户属性，并将其转换为 LexisNexis API 可以使用的格式。 然后，将其发送到 LexisNexis。  
| 4. | LexisNexis 会使用该信息并处理它，以根据风险分析来验证用户标识。 然后，它会将结果返回给中间层 API。
| 5. | 中间层 API 处理信息并将相关信息发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收来自中间层 API 的信息。 如果显示失败响应，则向用户显示一条错误消息。 如果它显示成功响应，则用户经过身份验证并获得访问权限。

## <a name="onboard-with-lexisnexis"></a>内置 LexisNexis

1. 若要创建 LexisNexis 帐户，请联系 [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. 创建符合要求的 LexisNexis 策略。 使用 [此处](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)提供的文档。

>[!NOTE]
> 稍后将使用该策略的名称。

创建帐户后，你将收到 API 配置所需的信息。 以下各节介绍了该过程。

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>配置 LexisNexis Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) 部署到 Azure 服务。 可以按照这些 [说明](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)从 Visual Studio 发布代码。

>[!NOTE]
>需要部署的服务的 URL 来配置 Azure AD，并提供所需的设置。

### <a name="part-2---configure-the-api"></a>第2部分-配置 API

可以 [在 Azure 中的应用服务中配置](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)应用程序设置。  使用此方法，可以安全地配置设置，而无需将其签入存储库。 需要为 Rest API 提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|ThreatMetrix： Url | ThreatMetrix 帐户配置 |     |
|ThreatMetrix： OrgId | ThreatMetrix 帐户配置 |     |
|ThreatMetrix： ApiKey |ThreatMetrix 帐户配置|  |
|ThreatMetrix：策略 | 在 ThreatMetrix 中创建的策略的名称 | |
| BasicAuth:ApiUsername |定义 API 的用户名| 用户名将用于 Azure AD B2C 配置
| BasicAuth:ApiPassword | 定义 API 的密码 | 将在 Azure AD B2C 配置中使用密码

### <a name="part-3---deploy-the-ui"></a>第3部分-部署 UI

此解决方案使用由 Azure AD B2C 加载的自定义 UI 模板。 这些 UI 模板会执行直接发送到 ThreatMetrix 服务的分析。

请参阅这些 [说明](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#custom-page-content-walkthrough) ，将包括的 [UI 文件](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) 部署到 blob 存储帐户。 说明包括设置 blob 存储帐户、配置 CORS 和启用公共访问。

UI 基于 [海蓝色模板](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue)。 UI 中的所有链接都应更新以引用部署的位置。 在 UI 文件夹中，查找并 https://yourblobstorage/blobcontainer 将替换为部署位置。

### <a name="part-4---create-api-policy-keys"></a>第4部分-创建 API 策略密钥

请参阅本 [文档](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) 并创建两个策略密钥–一个用于 api 用户名，另一个用于前面定义的 api 密码。

示例策略使用这些密钥名称：

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>第5部分-更新 API URL

在提供的 [trustframeworkextensions.xml 策略](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)中，找到名为的技术配置文件 `Rest-LexisNexus-SessionQuery` ，并将 `ServiceUrl` 元数据项目更新为上面部署的 API 的位置。

### <a name="part-6---update-ui-url"></a>第6部分-更新 UI URL

在提供的 [trustframeworkextensions.xml 策略](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)中，执行查找和替换搜索，并将 https://yourblobstorage/blobcontainer/ UI 文件部署到的位置搜索。

>[!NOTE]
> 作为最佳做法，我们建议客户在 "属性集合" 页中添加许可通知。 通知用户信息将发送到第三方服务进行身份验证。

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>第7部分-配置 Azure AD B2C 策略

请参阅此 [文档](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 下载 [本地帐户初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) ，并为 Azure AD B2C 租户配置 [策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) 。

>[!NOTE]
>更新提供的策略，使其与你的特定租户相关。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，并在 "策略" 下选择 " **用户流**"。

2. 选择以前创建的 **用户流**。

3. 选择 " **运行用户流** "，然后选择设置：

   a. **应用程序**：选择注册应用 (示例为 JWT) 

   b. **回复 url**：选择 "**重定向 url** "

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户

5. 注销

6. 经历登录流  

7. 输入 **continue**后，ThreatMetrix 谜会弹出。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
