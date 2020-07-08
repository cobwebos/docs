---
title: 设置身份验证
titleSuffix: Azure Machine Learning
description: 了解如何为 Azure 机器学习中的各种资源和工作流设置和配置身份验证。 可以通过多种方式在服务中配置和使用身份验证，范围从出于开发或测试目的而进行的基于 UI 的简单身份验证，到完整的 Azure Active Directory 服务主体身份验证不等。
services: machine-learning
author: larryfr
ms.author: larryfr
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 34641e7a883f6b07fe63595cf5750df2569640f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974681"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>为 Azure 机器学习资源和工作流设置身份验证
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何对 Azure 机器学习工作区进行身份验证，以及如何对部署为 web 服务的模型进行身份验证。

通常，可将两种类型的身份验证用于 Azure 机器学习：

* __交互式__：在 Azure Active Directory 中使用帐户直接进行身份验证，或获取用于身份验证的令牌。 在试验和迭代开发期间使用交互式身份验证。 或者您想要基于每个用户控制对资源（如 web 服务）的访问。
* __服务主体__：在 Azure Active Directory 中创建一个服务主体帐户，并使用该帐户进行身份验证或获取令牌。 当你需要自动过程向服务进行身份验证而无需用户交互时，可以使用服务主体。 例如，一种持续集成和部署脚本，每次定型代码发生更改时均定型和测试模型。 如果你不希望服务的最终用户进行身份验证，则还可以使用服务主体检索用于向 web 服务进行身份验证的令牌。 或者，最终用户身份验证不会使用 Azure Active Directory 直接执行。

无论使用何种身份验证类型，都可以使用基于角色的访问控制（RBAC）来确定允许访问资源的访问级别。 例如，用于获取已部署模型的访问令牌的帐户只需要对工作区的读取访问权限。 有关 RBAC 的详细信息，请参阅[管理对 Azure 机器学习工作区的访问](how-to-assign-roles.md)。

## <a name="prerequisites"></a>先决条件

* 创建 [Azure 机器学习工作区](how-to-manage-workspace.md)。
* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK，或使用已安装 SDK 的 [Azure 机器学习 Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance)。

## <a name="interactive-authentication"></a>交互式身份验证

文档和示例中的大多数示例都使用交互式身份验证。 例如，使用 SDK 时，有两个函数调用会自动提示你使用基于 UI 的身份验证流：

* 调用 `from_config()` 函数会发出提示。

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    `from_config()` 函数查找包含工作区连接信息的 JSON 文件。

* 使用 `Workspace` 构造函数提供订阅、资源组和工作区信息时，还会提示进行交互式身份验证。

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> 如果你可以访问多个租户，则可能需要导入类并显式定义目标租户。 与上述调用类似，为 `InteractiveLoginAuthentication` 调用构造函数也会提示你登录。
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>服务主体身份验证

若要使用服务主体（SP）身份验证，必须先创建 SP，并向其授予对工作区的访问权限。 如前文所述，使用 Azure 基于角色的访问控制（RBAC）来控制访问，因此您还必须确定授予 SP 的访问权限。

> [!IMPORTANT]
> 使用服务主体时，向其授予用于所__需任务的最低访问权限__。 例如，如果所有用户都用于读取 web 部署的访问令牌，则不会授予服务主体所有者或参与者访问权限。
>
> 授予最小访问权限的原因是服务主体使用密码进行身份验证，并且密码可能作为自动化脚本的一部分进行存储。 如果密码泄漏，则具有特定任务所需的最小访问权限可最大程度地减少对 SP 的恶意使用。

创建 SP 并授予对工作区的访问权限的最简单方法是使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 若要创建服务主体并授予它对工作区的访问权限，请执行以下步骤：

> [!NOTE]
> 若要执行所有这些步骤，你必须是订阅的管理员。

1. 对 Azure 订阅进行身份验证：

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，需要打开浏览器并按照命令行中的说明操作。 按照说明操作时，需要浏览到 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 并输入授权代码。

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    有关其他身份验证方法，请参阅[使用 Azure CLI 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

1. 安装 Azure 机器学习扩展：

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. 创建服务主体。 在以下示例中，创建了名为**ml 身份验证**的 SP：

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

1. 使用 `clientId` 上一步中返回的值检索服务主体的详细信息：

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    以下 JSON 是命令输出的简化示例。 记下 `objectId` 字段，因为在接下来的步骤中需要用到它的值。

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

1. 允许 SP 访问 Azure 机器学习工作区。 你将需要工作区名称，以及分别针对 `-w` 和 `-g` 参数的资源组名称。 对于 `--user` 参数，请使用之前步骤中的 `objectId` 值。 `--role`参数允许您为服务主体设置访问角色。 在以下示例中，将 SP 分配给**所有者**角色。 

    > [!IMPORTANT]
    > 所有者访问权限允许服务主体在工作区中执行几乎所有操作。 本文档中使用它来演示如何授予访问权限;在生产环境中，Microsoft 建议授予服务主体执行所需角色所需的最低访问权限。 有关详细信息，请参阅[管理对 Azure 机器学习工作区的访问](how-to-assign-roles.md)。

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    此调用在成功时不会生成任何输出。

### <a name="use-a-service-principal-from-the-sdk"></a>使用 SDK 中的服务主体

若要从 SDK 对工作区进行身份验证，请使用服务主体，并使用 `ServicePrincipalAuthentication` 类构造函数。 使用创建服务提供程序时获得的值作为参数。 `tenant_id` 参数映射到上述 `tenantId`，`service_principal_id` 映射到 `clientId`，而 `service_principal_password` 映射到 `clientSecret`。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 变量现在包含身份验证对象，可直接在 SDK 中使用。 一般而言，建议将上述 ID/机密存储在环境变量中，如下面的代码所示。 在环境变量中存储可防止将信息意外地签入 GitHub 存储库。

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

对于在 Python 中运行并主要使用 SDK 的自动化工作流，在大多数情况下，都可按原样使用此对象进行身份验证。 以下代码使用创建的身份验证对象向工作区进行身份验证。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>使用 Azure CLI 中的服务主体

可以将服务主体用于 Azure CLI 命令。 有关详细信息，请参阅[使用服务主体登录](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#sign-in-using-a-service-principal)。

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>将服务主体用于 REST API （预览）

还可以使用服务主体对 Azure 机器学习[REST API](https://docs.microsoft.com/rest/api/azureml/) （预览版）进行身份验证。 如果使用 Azure Active Directory [客户端凭据授予流](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)，则可在自动化工作流中进行无外设身份验证的服务到服务调用。 这些示例使用 Python 和 Node.js 中的 [ADAL 库](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)实现，但也可以使用支持 OpenID Connect 1.0 的任何开放源代码库。

> [!NOTE]
> MSAL.js 库比 ADAL 库更新，但不能配合使用客户端凭据和 MSAL.js 进行服务到服务的身份验证，因为它主要是一个客户端库，适用于与特定用户关联的交互式/UI 身份验证。 我们建议使用如下所示的 ADAL，通过 REST API 生成自动化工作流。

#### <a name="nodejs"></a>Node.js

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

变量 `tokenResponse` 是包含令牌和关联元数据（如过期时间）的对象。 令牌的有效时间为 1 小时，可以通过再次运行相同的调用检索新令牌来对令牌进行刷新。 以下代码片段是一个示例响应。

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

#### <a name="python"></a>Python

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

变量 `token_response` 是包含令牌和关联元数据（如过期时间）的字典。 令牌的有效时间为 1 小时，可以通过再次运行相同的调用检索新令牌来对令牌进行刷新。 以下代码片段是一个示例响应。

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

Azure 机器学习创建的模型部署提供了两种身份验证方法：

* **基于键**：使用静态密钥对 web 服务进行身份验证。
* **基于令牌**：必须从工作区获取临时令牌，并使用该令牌向 web 服务进行身份验证。 此标记将在一段时间后过期，并且必须刷新才能继续使用 web 服务。

    > [!NOTE]
    > 基于令牌的身份验证仅在部署到 Azure Kubernetes 服务时可用。

### <a name="key-based-web-service-authentication"></a>基于密钥的 web 服务身份验证

在 Azure Kubernetes Service （AKS）上部署的 Web 服务在默认情况下*启用*基于密钥的身份验证。 默认情况下，Azure 容器实例（ACI）部署的服务已*禁用*基于密钥的身份验证，但你可以通过 `auth_enabled=True` 在创建 ACI web 服务时设置来启用它。 以下代码是在启用基于密钥的身份验证的情况下创建 ACI 部署配置的一个示例。

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

若要详细了解如何对已部署的模型进行身份验证，请参阅为[部署为 web 服务的模型创建客户端](how-to-consume-web-service.md)。

### <a name="token-based-web-service-authentication"></a>基于令牌的 Web 服务身份验证

如果为 Web 服务启用令牌身份验证，用户必须向 Web 服务提供 Azure 机器学习 JSON Web 令牌才能访问它。 令牌在指定的时间范围后过期，需要刷新才能继续调用。

* 部署到 Azure Kubernetes 服务时，令牌身份验证默认处于禁用状态。
* 部署到 Azure 容器实例时，不支持令牌身份验证。
* 令牌身份验证**不能与基于密钥的身份验证同时使用**。

若要控制令牌身份验证，请 `token_auth_enabled` 在创建或更新部署时使用参数：

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

如果启用了令牌身份验证，可以使用 `get_token` 方法检索 JSON Web (JWT) 令牌以及该令牌的到期时间：

> [!TIP]
> 如果使用服务主体获取令牌，并希望它具有检索令牌所需的最小访问权限，请向其分配工作区的“读取者”角色。

```python
token, refresh_by = aks_service.get_token()
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

* [如何在训练中使用机密](how-to-use-secrets-in-runs.md)。
* [训练并部署图像分类模型](tutorial-train-models-with-aml.md)。
* [使用部署为 Web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)。
