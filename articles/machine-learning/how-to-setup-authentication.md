---
title: 设置身份验证
titleSuffix: Azure Machine Learning
description: 了解如何为 Azure 机器学习中的各种资源和工作流设置和配置身份验证。 可以通过多种方式在服务中配置和使用身份验证，范围从出于开发或测试目的而进行的基于 UI 的简单身份验证，到完整的 Azure Active Directory 服务主体身份验证不等。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 6b2cfa85ea412a5ef8bda47a7ff6e99970ba6b0e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611834"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>为 Azure 机器学习资源和工作流设置身份验证
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何为 Azure 机器学习中的各种资源和工作流设置和配置身份验证。 可以通过多种方式向服务进行身份验证，范围从出于开发或测试目的而进行的基于 UI 的简单身份验证，到完整的 Azure Active Directory 服务主体身份验证不等。 本文还介绍 Web 服务身份验证的工作原理差异，以及如何向 Azure 机器学习 REST API 进行身份验证。

本操作指南演示如何执行以下任务：

* 使用交互式 UI 身份验证进行测试/开发
* 设置服务主体身份验证
* 向工作区进行身份验证
* 获取 Azure 机器学习 REST API 的 OAuth2.0 持有者类型令牌
* 了解 Web 服务身份验证

有关 Azure 机器学习中的安全性和身份验证的一般性概述，请参阅[概念文章](concept-enterprise-security.md)。

## <a name="prerequisites"></a>先决条件

* 创建 [Azure 机器学习工作区](how-to-manage-workspace.md)。
* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK，或使用已安装 SDK 的 [Azure 机器学习 Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance)。

## <a name="interactive-authentication"></a>交互式身份验证

此服务文档中的大部分示例都使用 Jupyter Notebook 中的交互式身份验证作为一种简单的测试和演示方法。 这是一种测试所生成内容的轻量级方法。 有两个函数调用会使用基于 UI 的身份验证流自动提示你。

调用 `from_config()` 函数会发出提示。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()` 函数查找包含工作区连接信息的 JSON 文件。 也可以使用 `Workspace` 构造函数显式指定连接详细信息，这也会提示进行交互式身份验证。 两个调用是等效的。

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

如果你可以访问多个租户，则可能需要导入类并显式定义目标租户。 与上述调用类似，为 `InteractiveLoginAuthentication` 调用构造函数也会提示你登录。

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

尽管交互式身份验证对测试和学习很有帮助，但它对于生成自动化工作流或无外设工作流没有什么帮助。 对于使用 SDK 的自动化流程，最佳方法是设置服务主体身份验证。

## <a name="set-up-service-principal-authentication"></a>设置服务主体身份验证

此流程对于启用与特定用户登录名相互独立的身份验证而言是必需的，以便在自动化工作流中向 Azure 机器学习 Python SDK 进行身份验证。 服务主体身份验证还允许[向 REST API 进行身份验证](#azure-machine-learning-rest-api-auth)。

若要设置服务主体身份验证，请先在 Azure Active Directory 中创建应用注册，然后授予应用对 ML 工作区的基于角色的访问权限。 完成此设置的最简单方法是通过 Azure 门户中的 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 执行操作。 登录到门户后，单击页面右上方姓名旁边的 `>_` 图标以打开 shell。

如果之前没有在 Azure 帐户中使用过 Cloud Shell，则需要创建一个存储帐户资源用于存储写入的所有文件。 此存储帐户通常会产生每月成本，但可忽略不计。 此外，如果之前未使用过机器学习扩展，请使用以下命令安装该扩展。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 必须是订阅的管理员才能执行以下步骤。

接下来，运行以下命令以创建服务主主体。 为主体提供名称，在本例中为 ml-auth。

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

输出将是类似如下的 JSON。 记下 `clientId`、`clientSecret` 和 `tenantId` 字段，因为在本文的其他步骤中需要用到它们。

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

接下来，运行以下命令，获取刚才创建的服务主体的详细信息，并使用上述 `clientId` 值作为 `--id` 参数的输入。

```azurecli-interactive
az ad sp show --id your-client-id
```

以下是来自命令的 JSON 输出的简化示例。 记下 `objectId` 字段，因为在接下来的步骤中需要用到它的值。

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

接下来，使用以下命令将服务主体访问权限分配给机器学习工作区。 你将需要工作区名称，以及分别针对 `-w` 和 `-g` 参数的资源组名称。 对于 `--user` 参数，请使用之前步骤中的 `objectId` 值。 可使用 `--role` 参数设置服务主体的访问角色，通常将使用“所有者”或“参与者”角色。 两者都具有现有资源（如计算机群集和数据存储）的写入访问权限，但仅“所有者”角色才能预配这些资源。

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

此调用不会生成任何输出，但你现在已为工作区设置好服务主体身份验证。

## <a name="authenticate-to-your-workspace"></a>向工作区进行身份验证

现在，你已启用服务主体身份验证，接下来可以在 SDK 中向工作区进行身份验证，而无需作为用户进行物理登录。 使用 `ServicePrincipalAuthentication` 类构造函数，并使用从之前步骤中获取的值作为参数。 `tenant_id` 参数映射到上述 `tenantId`，`service_principal_id` 映射到 `clientId`，而 `service_principal_password` 映射到 `clientSecret`。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 变量现在包含身份验证对象，可直接在 SDK 中使用。 一般而言，建议将上述 ID/机密存储在环境变量中，如下面的代码所示。

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

对于在 Python 中运行并主要使用 SDK 的自动化工作流，在大多数情况下，都可按原样使用此对象进行身份验证。 以下代码使用刚才创建的身份验证对象向工作区进行身份验证。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure 机器学习 REST API 身份验证

上述步骤中创建的服务主体也可用于向 Azure 机器学习 [REST API](https://docs.microsoft.com/rest/api/azureml/) 进行身份验证。 如果使用 Azure Active Directory [客户端凭据授予流](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)，则可在自动化工作流中进行无外设身份验证的服务到服务调用。 这些示例使用 Python 和 Node.js 中的 [ADAL 库](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)实现，但也可以使用支持 OpenID Connect 1.0 的任何开放源代码库。

> [!NOTE]
> MSAL.js 库比 ADAL 库更新，但不能配合使用客户端凭据和 MSAL.js 进行服务到服务的身份验证，因为它主要是一个客户端库，适用于与特定用户关联的交互式/UI 身份验证。 我们建议使用如下所示的 ADAL，通过 REST API 生成自动化工作流。

### <a name="nodejs"></a>Node.js

使用以下步骤，通过 Node.js 生成身份验证令牌。 在你的环境中运行 `npm install adal-node`。 然后，使用在上述步骤中创建的服务主体中的 `tenantId`、`clientId` 和 `clientSecret`，作为以下脚本中匹配变量的值。

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

变量 `tokenResponse` 是包含令牌和关联元数据（如过期时间）的对象。 令牌的有效时间为 1 小时，可以通过再次运行相同的调用检索新令牌来对令牌进行刷新。 以下是示例响应。

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

使用 `accessToken` 属性提取身份验证令牌。 有关如何使用令牌进行 API 调用的示例，请参阅 [REST API 文档](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

### <a name="python"></a>Python

使用以下步骤，通过 Python 生成身份验证令牌。 在你的环境中运行 `pip install adal`。 然后，使用在上述步骤中创建的服务主体中的 `tenantId`、`clientId` 和 `clientSecret`，作为以下脚本中相应变量的值。

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

变量 `token_response` 是包含令牌和关联元数据（如过期时间）的字典。 令牌的有效时间为 1 小时，可以通过再次运行相同的调用检索新令牌来对令牌进行刷新。 以下是示例响应。

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

使用 `token_response["accessToken"]` 提取身份验证令牌。 有关如何使用令牌进行 API 调用的示例，请参阅 [REST API 文档](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)。

## <a name="web-service-authentication"></a>Web 服务身份验证

Azure 机器学习中的 Web 服务使用的身份验证模式与上述身份验证模式不同。 若要向部署的 Web 服务进行身份验证，最简单的方法是使用**基于密钥的身份验证**，这种身份验证会生成静态持有者类型身份验证密钥，并且无需刷新。 如果只需向部署的 Web 服务进行身份验证，则无需设置如上所示的服务主体身份验证。

部署在 Azure Kubernetes 服务上的 Web 服务默认启用基于密钥的身份验证。 Azure 容器实例部署的服务默认禁用基于密钥的身份验证，但你可以在创建 ACI Web 服务时通过设置 `auth_enabled=True` 来启用它。 以下是创建已启用基于密钥的身份验证的 ACI 部署配置的示例。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

然后可以通过 `Model` 类在部署中使用自定义 ACI 配置。

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

若要提取身份验证密钥，请使用 `aci_service.get_keys()`。 若要重新生成密钥，请使用 `regen_key()` 函数并传递 Primary 或 Secondary 。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Web 服务还支持基于令牌的身份验证，但仅适用于 Azure Kubernetes 服务部署。 有关身份验证的其他信息，请参阅关于使用 Web 服务的[操作指南](how-to-consume-web-service.md)。

### <a name="token-based-web-service-authentication"></a>基于令牌的 Web 服务身份验证

如果为 Web 服务启用令牌身份验证，用户必须向 Web 服务提供 Azure 机器学习 JSON Web 令牌才能访问它。 令牌在指定的时间范围后过期，需要刷新才能继续调用。

* 部署到 Azure Kubernetes 服务时，令牌身份验证默认处于禁用状态。
* 部署到 Azure 容器实例时，不支持令牌身份验证。

要控制令牌身份验证，请在创建或更新部署时使用 `token_auth_enabled` 参数。

如果启用了令牌身份验证，可以使用 `get_token` 方法检索 JSON Web (JWT) 令牌以及该令牌的到期时间：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌的 `refresh_by` 时间后请求一个新令牌。 如果需要在 Python SDK 之外刷新令牌，一个选择是使用具有服务主体身份验证的 REST API 定期进行 `service.get_token()` 调用，如前文所述。
>
> 我们强烈建议在 Azure Kubernetes 服务群集所在的同一区域中创建 Azure 机器学习工作区。
>
> 若要使用令牌进行身份验证，Web 服务将调用在其中创建 Azure 机器学习工作区的区域。 如果工作区的区域不可用，即使你的群集和工作区不在同一区域，你也无法提取 Web 服务的令牌。 因此，直到工作区的区域再次可用时，Azure AD 身份验证才会可用。
>
> 此外，群集区域和工作区区域的距离越远，提取令牌所需的时间就越长。

## <a name="next-steps"></a>后续步骤

* [训练并部署图像分类模型](tutorial-train-models-with-aml.md)。
* [使用部署为 Web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)。
