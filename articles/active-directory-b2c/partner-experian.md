---
title: 通过 Experian 配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与 Experian 集成以进行身份验证，并基于用户属性进行校对以防止欺诈行为。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a88894bb7462e9ac3afd16d69ae820dd98543a5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259367"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>有关配置 Experian 与 Azure Active Directory B2C 的教程

在此示例教程中，我们提供了有关如何将 Azure AD B2C 与 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)集成的指导。 Experian 提供多种解决方案，可在 [此处](https://www.experian.com/)找到。

在此示例中，使用了 Experian 的集成数字标识和欺诈风险平台 **CrossCore** 。 CrossCore 是用于验证用户标识的 ID 验证服务。 它根据用户在注册流期间提供的信息来进行风险分析。 CrossCore 用来确定是否应允许用户继续登录。 以下属性可用于 CrossCore 风险分析：

- 电子邮件
- IP 地址
- 名
- Middle Name
- Surname
- 街道地址
- 城市
- 省/自治区/直辖市
- 邮政编码
- 国家/地区
- 电话号码

## <a name="prerequisites"></a>必备条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的[Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。

## <a name="scenario-description"></a>方案描述

Experian 集成包括以下组件：

- Azure AD B2C –授权服务器，负责验证用户的凭据，也称为标识提供者

- Experian – Experian 服务获取用户提供的输入，并验证用户的身份

- 自定义 Rest API –此 API 实现 Azure AD B2C 与 Experian 服务之间的集成。

下面的体系结构关系图显示了实现。

![experian 的屏幕截图](media/partner-experian/experian-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户选择 "注册" 以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用中间层 API 并传递用户特性。
| 3. | 中间层 API 收集用户属性，并将其转换为 Experian API 可以使用的格式。 然后，将其发送到 Experian。
| 4. | Experian 会使用该信息并处理它，以根据风险分析来验证用户标识。 然后，它会将结果返回给中间层 API。
| 5. | 中间层 API 处理信息并以正确的 JSON 格式将相关信息发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收来自中间层 API 的信息。 如果显示失败响应，则向用户显示一条错误消息。 如果它显示成功响应，则会对用户进行身份验证并将其写入到目录中。

## <a name="onboard-with-experian"></a>内置 Experian

1. 若要创建 Experian 帐户，请联系 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. 创建帐户后，你将收到 API 配置所需的信息。 以下各节介绍了该过程。

## <a name="configure-azure-ad-b2c-with-experian"></a>配置 Experian Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) 部署到 Azure 服务。 可以按照这些 [说明](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)从 Visual Studio 发布代码。

>[!NOTE]
>需要部署的服务的 URL 来配置 Azure AD，并提供所需的设置。

### <a name="part-2---deploy-the-client-certificate"></a>第2部分-部署客户端证书

Experian API 调用受到客户端证书的保护。 此客户端证书将由 Experian 提供。 按照本 [文档](https://docs.microsoft.com/azure/app-service/environment/certificates#private-client-certificate)中所述的说明，证书必须上传到 Azure 应用服务。 示例策略在过程中使用以下键步骤：

- 上传证书

- 将 `WEBSITE_LOAD_ROOT_CERTIFICATES` 密钥设置为证书的指纹。

### <a name="part-3---configure-the-api"></a>第3部分-配置 API

可以 [在 Azure 中的应用服务中配置](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)应用程序设置。 使用此方法，可以安全地配置设置，而无需将其签入存储库。 需要为 Rest API 提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|CrossCoreConfig： TenantId | Experian 帐户配置 |     |
|CrossCoreConfig:OrgCode | Experian 帐户配置 |     |
|CrossCore： ApiEndpoint |Experian 帐户配置|  |
|CrossCore:ClientReference | Experian 帐户配置 | |
| CrossCore:ModelCode |Experian 帐户配置|
| CrossCore:OrgCode | Experian 帐户配置 |
| CrossCore:SignatureKey  | Experian 帐户配置 |
| CrossCore： TenantId  | Experian 帐户配置 |
| CrossCore： CertificateThumbprint | Experian 证书 |
| BasicAuth:ApiUsername | 定义 API 的用户名 | 在 ExtId 配置中使用 |
| BasicAuth:ApiPassword | 定义 API 的密码 | 在 ExtId 配置中使用

### <a name="part-4---create-api-policy-keys"></a>第4部分-创建 API 策略密钥

请参阅本 [文档](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) 并创建两个策略密钥–一个用于 api 用户名，另一个用于为 HTTP 基本身份验证定义的 api 密码。

>[!NOTE]
>稍后需要用到的密钥来配置策略。

### <a name="part-5---replace-the-configuration-values"></a>第5部分-替换配置值

在提供的 [自定义策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)中，查找以下占位符，并将替换为实例中的相应值

|                      占位符                       |                                   替换为值                                 |                   示例                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | 租户短名称                                                           | yourtenant.onmicrosoft.com 的 "yourtenant" |
| {your_trustframeworkbase_policy}                       | Trustframeworkbase.xml 策略的 Azure AD B2C 名称                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 租户中配置的 IdentityExperienceFramework 应用的应用 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 租户中配置的 ProxyIdentityExperienceFramework 应用的应用 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租户的存储应用程序的应用 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租户的存储应用程序的对象 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | [此处](#part-4---create-api-policy-keys)创建的用户名密钥的名称             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | 在[此处](#part-4---create-api-policy-keys)创建的密码密钥的名称             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | 已设置的应用服务的 URL                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>第6部分-配置 Azure AD B2C 策略

请参阅本 [文档](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) ，了解有关如何设置 Azure AD B2C 租户和配置策略的说明。

>[!NOTE]
>此示例策略基于 [本地帐户 starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)。

>[!NOTE]
> 作为最佳做法，我们建议客户在 "属性集合" 页中添加许可通知。 通知用户信息将发送到第三方服务进行身份验证。

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

7. 输入 **continue**后，CrossCore 谜会弹出。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
