---
title: 通过 Onfido 配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 了解如何通过 Onfido 将 Azure AD B2C authentication 与文档 ID 和面部生物识别验证集成
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5d0835114844069d4ebdc992b872f9be1f0b3ca6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259214"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>有关配置 Onfido 与 Azure Active Directory B2C 的教程

在此示例教程中，我们提供了有关如何将 Azure AD B2C 与 [Onfido](https://onfido.com/)集成的指导。 Onfido 是文档 ID 和面部生物识别验证应用。 它允许公司实时 *了解你的客户* 和标识要求。 Onfido 使用基于 AI 的复杂身份验证，该验证首先验证照片 ID，然后将其与面部生物识别进行匹配。 此解决方案将数字标识与真实人员联系在一起，并在降低欺诈的同时提供安全的载入体验。

在此示例中，我们在注册或登录流中连接 Onfido 的服务，以进行身份验证。 根据 Onfido 的结果，确定用户可以访问的产品和服务的明智决策。

## <a name="prerequisites"></a>必备条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的[Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。

- Onfido [试用帐户](https://onfido.com/signup/)。

## <a name="scenario-description"></a>方案描述

Onfido 集成包括以下组件：

- Azure AD B2C 租户–授权服务器，负责根据租户中定义的自定义策略来验证用户的凭据。 它也称为标识提供者。 它承载了 Onfido 客户端应用程序，该应用程序收集用户文档并将其传输到 Onfido API 服务。

- Onfido 客户端–一种可配置的 JavaScript 客户端文档集合实用工具，部署在其他网页中。 收集文档并执行初步检查，如文档大小和质量。

- 中间 Rest API –提供 Azure AD B2C 租户的终结点，以便与 Onfido API 服务通信，处理数据处理并遵守这两项的安全要求。

- Onfido API service – Onfido 提供的后端服务，用于保存和验证用户提供的文档。

下面的体系结构关系图显示了实现。

![onfido 的屏幕截图](media/partner-onfido/onfido-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户注册以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。 Azure AD B2C 中托管的 Onfido 客户端应用程序会初步检查用户信息。
| 2. | Azure AD B2C 调用中间层 API 并传递用户特性。
| 3. | 中间层 API 收集用户属性，并将其转换为 Onfido API 可以使用的格式。 然后，将其发送到 Onfido。
| 4. | Onfido 会使用该信息并对其进行处理来验证用户标识。 然后，它会将结果返回给中间层 API。
| 5. | 中间层 API 处理信息并以正确的 JSON 格式将相关信息发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收来自中间层 API 的信息。 如果显示失败响应，则向用户显示一条错误消息。 如果它显示成功响应，则会对用户进行身份验证并将其写入到目录中。

## <a name="onboard-with-onfido"></a>内置 Onfido

1. 若要创建 Onfido 帐户，请联系 [Onfido](https://onfido.com/signup/)。

2. 创建帐户后，创建 [API 密钥](https://documentation.onfido.com/)。 实时密钥是可计费的，但是，你可以使用 [沙盒密钥来测试](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) 解决方案。 沙盒密钥生成与实时密钥相同的结果结构，但是，结果始终是预先确定的。 不处理或保存文档。

>[!NOTE]
> 稍后将需要此密钥。

有关 Onfido 的详细信息，请参阅 [ONFIDO API 文档](https://documentation.onfido.com) 和 [Onfido 开发人员中心](https://developers.onfido.com)。  

## <a name="configure-azure-ad-b2c-with-onfido"></a>配置 Onfido Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

- 将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) 部署到 Azure 服务。 可以按照这些 [说明](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)从 Visual Studio 发布代码。
- 设置 CORS，将允许的 **源** 添加为 https：//{your_tenant_name}. b2clogin .com

>[!NOTE]
>需要部署的服务的 URL 来配置 Azure AD，并提供所需的设置。

#### <a name="adding-sensitive-configuration-settings"></a>添加敏感配置设置

可以在 [Azure 中的应用服务](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)中配置应用程序设置。 应用服务允许安全配置设置，而无需将其签入存储库。 Rest API 需要以下设置：

| 应用程序设置名称 | Source | 注释 |
|:-------------------------|:-------|:-------|
|OnfidoSettings： AuthToken| Onfido 帐户 |

### <a name="part-2---deploy-the-ui"></a>第2部分-部署 UI

#### <a name="configure-your-storage-location"></a>配置存储位置

1. [在存储帐户中设置 blob 存储容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)

2. 将 ui 文件从 [ui 文件夹](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) 存储到 blob 容器。

3. 按照以下说明允许 CORS 对所创建存储容器的访问权限：

   a. 请在 "**设置**  > **允许的源**" 中输入 `https://{your_tenant_name}.b2clogin.com` 。 将你的租户-name 替换为你的 Azure AD B2C 租户的名称。 例如， https://fabrikam.b2clogin.com 。 输入租户名称时全部使用小写字母。

   b. 对于 **允许的方法**，请选择 `GET` 和 `PUT` 。

   c. 选择“保存”。

#### <a name="update-ui-files"></a>更新 UI 文件

1. 在 UI 文件中，切换到文件夹[ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. 打开每个 html 文件。

3. 查找并将 {ui blob-url} 替换为 UI **ocean_blue**、 **dist**和 **资产** 文件夹所在位置的 url

4. 查找并将 {中间 api url} 替换为中间 API 应用服务的 URL。

#### <a name="upload-your-files"></a>上传文件

1. 将 ui 文件从 UI 文件夹存储到 blob 容器。

2. 使用 [Azure 存储资源管理器](https://docs.microsoft.com/azure/virtual-machines/windows/disks-use-storage-explorer-managed-disks) 管理文件和访问权限。

### <a name="part-3---configure-azure-ad-b2c"></a>第3部分-配置 Azure AD B2C

#### <a name="replace-the-configuration-values"></a>替换配置值

在提供的 [自定义策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)中，查找以下占位符，并将替换为您的实例中的相应值。

| 占位符 | 替换为值 | 示例  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | 租户短名称 |  yourtenant.onmicrosoft.com 的 "yourtenant" |
| {your_tenantID} | Azure AD B2C 租户的 TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 租户中配置的 IdentityExperienceFramework 应用的应用 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 租户中配置的 ProxyIdentityExperienceFramework 应用的应用 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租户的存储应用程序的应用 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租户的存储应用程序的对象 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | App insights 实例的检测密钥 *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| UI **ocean_blue**、 **dist**和 **资产** 文件夹所在位置的 URL | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | 已设置的应用服务的 URL                                             | `https://yourapp.azurewebsites.net`          |

* App insights 可以在不同的租户中。 此步骤是可选的。 删除相应的技术配置文件和 OrchestrationSteps （如果不需要）。

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第4部分-配置 Azure AD B2C 策略

请参阅本 [文档](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) ，了解有关如何设置 Azure AD B2C 租户和配置策略的说明。

>[!NOTE]
> 作为最佳做法，我们建议客户在 "属性集合" 页中添加许可通知。 通知用户信息将发送到第三方服务进行身份验证。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，并在 "策略" 下选择 " **标识体验框架**"。

2. 选择先前创建的 **SignUpSignIn**。

3. 选择 " **运行用户流** "，然后选择设置：

   a. **应用程序**：选择注册应用 (示例为 JWT) 

   b. **回复 url**：选择 "**重定向 url** "

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户

5. 创建用户属性后，将在流中调用 Onfido 服务。 如果流未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
