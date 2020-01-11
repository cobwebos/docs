---
title: 设置身份验证
titleSuffix: Azure Machine Learning
description: 了解如何为 Azure 机器学习中的各种资源和工作流设置和配置身份验证。 可以通过多种方式在服务中配置和使用身份验证，范围从用于开发或测试的简单的基于 UI 的身份验证到完全 Azure Active Directory 的服务主体身份验证。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: ce85c45d80a776af84a0987cfbc3f496c2bbb72b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893961"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>为 Azure 机器学习资源和工作流设置身份验证
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何为 Azure 机器学习中的各种资源和工作流设置和配置身份验证。 向服务进行身份验证的方式有多种，范围从用于开发或测试的简单的基于 UI 的身份验证到完全 Azure Active Directory 的服务主体身份验证。 本文还介绍了 web 服务身份验证的工作原理，以及如何对 Azure 机器学习 REST API 进行身份验证。

本操作指南说明如何执行以下任务：

* 使用交互式 UI 身份验证进行测试/开发
* 设置服务主体身份验证
* 向工作区进行身份验证
* 获取 Azure 机器学习 REST API 的 OAuth 2.0 持有者类型令牌
* 了解 web 服务身份验证

有关 Azure 机器学习中的安全性和身份验证的一般概述，请参阅[概念一文](concept-enterprise-security.md)。

## <a name="prerequisites"></a>必备组件

* 创建 [Azure 机器学习工作区](how-to-manage-workspace.md)。
* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK，或使用已安装 sdk 的[Azure 机器学习笔记本 VM](concept-azure-machine-learning-architecture.md#compute-instance) 。

## <a name="interactive-authentication"></a>交互式身份验证

此服务的文档中的大多数示例都在 Jupyter 笔记本中使用交互式身份验证作为测试和演示的一种简单方法。 这是一种用于测试正在生成的内容的轻型方法。 有两个函数调用会自动提示您使用基于 UI 的身份验证流。

调用 `from_config()` 函数将发出提示。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()` 函数查找包含你的工作区连接信息的 JSON 文件。 还可以通过使用 `Workspace` 构造函数（该构造函数也会提示进行交互式身份验证）来显式指定连接详细信息。 这两个调用是等效的。

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

如果有权访问多个租户，则可能需要导入类并显式定义目标租户。 为 `InteractiveLoginAuthentication` 调用构造函数还会提示您登录，就像上述调用一样。

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

交互式身份验证对测试和学习非常有用，但它不能帮助你构建自动或无外设的工作流。 设置服务主体身份验证是使用 SDK 的自动化过程的最佳方法。

## <a name="set-up-service-principal-authentication"></a>设置服务主体身份验证

此过程对于启用与特定用户登录名分离的身份验证是必需的，这允许您在自动工作流中对 Azure 机器学习 Python SDK 进行身份验证。 利用服务主体身份验证，你还可以对[REST API 进行身份](#azure-machine-learning-rest-api-auth)验证。

若要设置服务主体身份验证，请先在 Azure Active Directory 中创建应用注册，然后向你的 ML 工作区授予应用基于角色的访问权限。 完成此设置的最简单方法是通过 Azure 门户中的[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 。 登录到门户后，单击页面右上角的 "`>_`" 图标以打开外壳。

如果尚未在 Azure 帐户中使用 cloud shell，则需要创建存储帐户资源来存储写入的任何文件。 通常，此存储帐户将产生每月可忽略的每月成本。 此外，如果以前未使用过以下命令，请安装机器学习扩展。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 若要执行以下步骤，你必须是订阅的管理员。

接下来，运行以下命令来创建服务主体。 为其指定一个名称，在本例中为**ml 身份验证**。

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

输出将为类似于下面的 JSON。 记下 "`clientId`"、"`clientSecret`" 和 "`tenantId`" 字段，因为本文中的其他步骤需要用到它们。

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

接下来，运行以下命令以获取有关刚刚创建的服务主体的详细信息，并使用上述 `clientId` 值作为 `--id` 参数的输入。

```azurecli-interactive
az ad sp show --id your-client-id
```

下面是命令的 JSON 输出的简化示例。 记下 "`objectId`" 字段，因为下一步需要用到它的值。

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

接下来，使用以下命令将你的服务主体访问权限分配给你的机器学习工作区。 将分别需要 `-w` 和 `-g` 参数的工作区名称和资源组名称。 对于 `--user` 参数，请使用上一步中的 `objectId` 值。 `--role` 参数允许您为服务主体设置访问角色，而在一般情况下，您将使用 "**所有者**" 或 "**参与者**"。 两者都具有对现有资源（例如计算群集和数据存储）的写入访问权限，但只有**所有者**可以预配这些资源。 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

此调用不会生成任何输出，但你现在已为你的工作区设置了服务主体身份验证。

## <a name="authenticate-to-your-workspace"></a>向工作区进行身份验证

启用服务主体身份验证后，可以在 SDK 中对工作区进行身份验证，而无需以用户身份进行物理登录。 使用 `ServicePrincipalAuthentication` 类构造函数，并使用前面步骤中获取的值作为参数。 `tenant_id` 参数映射到上述 `tenantId`，`service_principal_id` 映射到 `clientId`，`service_principal_password` 映射到 `clientSecret`。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 变量现在包含直接在 SDK 中使用的身份验证对象。 通常情况下，最好将上述 id/机密存储在环境变量中，如下面的代码所示。

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

对于在 Python 中运行并主要使用 SDK 的自动工作流，在大多数情况下，你可以在身份验证中按原样使用此对象。 以下代码使用刚刚创建的身份验证对象对工作区进行身份验证。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>REST API 身份验证 Azure 机器学习

在上述步骤中创建的服务主体也可用于对 Azure 机器学习[REST API](https://docs.microsoft.com/rest/api/azureml/)进行身份验证。 使用 Azure Active Directory[客户端凭据授予流](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)，这允许在自动工作流中进行无外设身份验证的服务到服务调用。 这些示例是在 Python 和 node.js 中通过[ADAL 库](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)实现的，但也可以使用支持 OpenID connect 1.0 的任何开放源代码库。 

> [!NOTE]
> MSAL 是一个比 ADAL 更高的库，但不能使用 MSAL 的客户端凭据执行服务到服务身份验证，因为它主要是一个客户端库，适用于与特定用户关联的交互/UI 身份验证。 建议使用 ADAL （如下所示），通过 REST API 生成自动化工作流。

### <a name="nodejs"></a>Node.js

使用以下步骤，通过 node.js 生成身份验证令牌。 在环境中，运行 `npm install adal-node`。 然后，使用在上述步骤中创建的服务主体的 `tenantId`、`clientId`和 `clientSecret`，作为以下脚本中匹配变量的值。

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

变量 `tokenResponse` 是包含标记和关联的元数据（如过期时间）的对象。 令牌的有效时间为1小时，可通过再次运行同一调用来刷新，以检索新令牌。 下面是一个示例响应。

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

使用 `accessToken` 属性获取身份验证令牌。 请参阅[REST API 文档](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)，以获取有关如何使用令牌进行 API 调用的示例。

### <a name="python"></a>Python 

按照以下步骤使用 Python 生成身份验证令牌。 在环境中，运行 `pip install adal`。 然后，使用在上述步骤中创建的服务主体的 `tenantId`、`clientId`和 `clientSecret`，作为以下脚本中相应变量的值。

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

变量 `token_response` 是包含标记和关联的元数据（如过期时间）的字典。 令牌的有效时间为1小时，可通过再次运行同一调用来刷新，以检索新令牌。 下面是一个示例响应。

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

使用 `token_response["accessToken"]` 获取身份验证令牌。 请参阅[REST API 文档](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)，以获取有关如何使用令牌进行 API 调用的示例。

## <a name="web-service-authentication"></a>Web 服务身份验证

Azure 机器学习中的 Web 服务使用不同于上面所述的身份验证模式。 向部署的 web 服务进行身份验证的最简单方法是使用**基于密钥的身份验证，该身份验证**将生成无需刷新的静态持有者类型身份验证密钥。 如果只需要对已部署的 web 服务进行身份验证，则无需设置服务主体身份验证，如上所述。

默认情况下，在 Azure Kubernetes Service 上部署的 Web 服务已*启用*基于密钥的身份验证。 默认情况下，Azure 容器实例部署的服务已*禁用*基于密钥的身份验证，但你可以通过在创建 ACI web 服务时设置 `auth_enabled=True`来启用它。 下面是一个示例，用于创建启用了基于密钥的身份验证的 ACI 部署配置。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enable=True)
```

然后，你可以使用 `Model` 类在部署中使用自定义 ACI 配置。

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

若要获取身份验证密钥，请使用 `aci_service.get_keys()`。 若要重新生成密钥，请使用 `regen_key()` 函数并传递**Primary**或**辅助**。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Web 服务还支持基于令牌的身份验证，但仅适用于 Azure Kubernetes 服务部署。 有关身份验证的其他信息，请参阅[如何](how-to-consume-web-service.md)使用 web 服务。

### <a name="token-based-web-service-authentication"></a>基于令牌的 web 服务身份验证

为 web 服务启用令牌身份验证时，用户必须向 web 服务提供 Azure 机器学习 JSON Web 令牌才能访问它。 令牌在指定的时间框架后过期，需要刷新才能继续调用。

* 部署到 Azure Kubernetes 服务时，令牌身份验证**默认情况下处于禁用状态**。
* 当你部署到 Azure 容器实例时，令牌身份验证**不受支持**。

若要控制令牌身份验证，请在创建或更新部署时使用 `token_auth_enabled` 参数。

如果启用了令牌身份验证，则可以使用 `get_token` 方法检索 JSON Web 令牌（JWT）和令牌的过期时间：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌的 `refresh_by` 时间之后请求新令牌。 如果需要刷新 Python SDK 之外的令牌，一种选择是使用服务主体身份验证的 REST API 定期进行 `service.get_token()` 调用，如前文所述。
>
> 强烈建议在 Azure Kubernetes Service 群集所在的同一区域中创建 Azure 机器学习工作区。 
>
> 若要使用令牌进行身份验证，web 服务将调用创建 Azure 机器学习工作区的区域。 如果工作区的区域不可用，则即使群集与工作区位于不同的区域，也无法获取 web 服务的令牌。 结果是，在工作区的区域再次可用之前，Azure AD 身份验证不可用。 
>
> 此外，群集区域与工作区区域之间的距离越大，提取令牌所需的时间就越长。

## <a name="next-steps"></a>后续步骤

* [训练和部署图像分类模型](tutorial-train-models-with-aml.md)。
* [使用部署为 web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)。
