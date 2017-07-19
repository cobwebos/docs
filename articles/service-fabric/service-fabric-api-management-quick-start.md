---
title: "Azure Service Fabric 与 API 管理快速入门 | Microsoft Docs"
description: "本指南演示如何快速开始使用 Azure API 管理和 Service Fabric。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2160e2e65de5c65df8a13248bad4f626def86e49
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---

# <a name="service-fabric-with-azure-api-management-quick-start"></a>Service Fabric 与 Azure API 管理快速入门

本指南演示如何设置 Azure API 管理和 Service Fabric，并配置你的第一个 API 操作，从而将流量发送到 Service Fabric 中的后端服务。 若要详细了解使用 Service Fabric 的 Azure API 管理方案，请参阅[概述](service-fabric-api-management-overview.md)一文。 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>将 API 管理和 Service Fabric 部署到 Azure

第一步是将 API 管理和 Service Fabric 群集部署到共享虚拟网络中的 Azure。 这使 API 管理可以直接与 Service Fabric 通信，这样它就可以执行服务发现、服务分区解析，并将流量直接转发给 Service Fabric 中的任何后端服务。

### <a name="topology"></a>拓扑

本指南将以下拓扑部署到 Azure，其 中 API 管理和 Service Fabric 位于同一虚拟网络的子网中：

 ![图片题注][sf-apim-topology-overview]

要快速开始，请为每个部署步骤提供 Resource Manager 模板：

 - 网络拓扑：
    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]
 - Service Fabric 群集：
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]
 - API 管理：
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>登录到 Azure，然后选择订阅

本指南使用 [Azure PowerShell][azure-powershell]。 开始新的 PowerShell 会话时，请登录到 Azure 帐户并选择订阅，然后执行 Azure 命令。
 
请登录到 Azure 帐户：

```powershell
PS > Login-AzureRmAccount
```

选择订阅：

```powershell
PS > Get-AzureRmSubscription
PS > Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>创建资源组

为部署创建新的资源组。 为该资源组提供一个名称和位置。

```powershell
PS > New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>部署网络拓扑

第一步是设置将向其部署 API 管理和 Service Fabric 群集的网络拓扑。 配置 [network.json][network-arm] Resource Manager 模板以创建虚拟网络 (VNET)，其中包括两个子网和两个网络安全组 (NSG)。 

[network.parameters.json][network-parameters-arm] 参数文件包含子网名称以及将向其部署 API 管理和 Service Fabric 的 NSG。 本指南中，不需要更改参数值。 API 管理和 Service Fabric Resource Manager 模板使用这些值，因此如果在此处进行修改，必须相应地在其他 Resource Manager 模板中对其进行修改。 

 1. 下载以下 Resource Manager 模板和参数文件：

    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]

 2. 使用以下 PowerShell 命令为网络设置部署 Resource Manager 模板和参数文件：

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>部署 Service Fabric 群集

网络资源部署完成后，下一步是将 Service Fabric 群集部署到子网中的 VNET 以及为 Service Fabric 群集指定的 NSG。 在本教程中，预配置 Service Fabric Resource Manager 模板以使用上一步中设置的 VNET、子网和 NSG 的名称。 

配置 Service Fabric 群集 Resource Manager 模板以创建具备证书安全性的安全群集。 证书用于保护群集的节点到节点通信，并管理用户对你的 Service Fabric 群集的访问权限。 API 管理使用此证书来访问服务发现的 Service Fabric 命名服务。

此步骤需要具备密钥保管库中的群集安全性证书。 有关使用密钥保管库设置安全群集的详细信息，请参阅[本指南中有关在 Azure 中使用 Resource Manager 创建群集的内容](service-fabric-cluster-creation-via-arm.md)

> [!NOTE]
> 除了用于群集访问的证书之外，还可以添加 Azure Active Directory 身份验证。 建议使用 Azure Active Directory 来管理用户对你的 Service Fabric 群集的访问权限，但不需要完成本教程。 无论是群集节点到节点安全性还是 Azure API 管理身份验证，都需要提供证书，但后者目前不支持使用 Azure Active Directory 对 Service Fabric 后端进行身份验证。

 1. 下载以下 Resource Manager 模板和参数文件：
 
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]

 2. 针对部署填充 `cluster.parameters.json` 文件中的空参数，其中包括群集证书的[密钥保管库信息](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault)。

 3. 使用以下 PowerShell 命令部署 Resource Manager 模板和参数文件，以创建 Service Fabric 群集：

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>部署 API 管理

最后，将 API 管理部署到子网中的 VNET 以及为 API 管理指定的 NSG。 在部署 API 管理之前，不需要等待 Service Fabric 群集部署完成。 

在本教程中，预配置 API 管理 Resource Manager 模板以使用上一步中设置的 VNET、子网和 NSG 的名称。 

 1. 下载以下 Resource Manager 模板和参数文件：
 
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

 2. 针对部署填充 `apim.parameters.json` 中的空参数。

 3. 使用以下 PowerShell 命令为 API 管理部署 Resource Manager 模板和参数文件：

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>配置 API 管理

在部署 API 管理和 Service Fabric 群集后，可以在 API 管理中配置 Service Fabric 后端。 这样，你就可以创建后端服务策略，将流量发送到 Service Fabric 群集。

### <a name="api-management-security"></a>API 管理安全性

要配置 Service Fabric 后端，首先需要配置 API 管理安全性设置。 要配置安全性设置，请转到 Azure 门户中的 API 管理服务。

#### <a name="enable-the-api-management-rest-api"></a>启用 API 管理 REST API

API 管理 REST API 是目前配置后端服务的唯一方法。 第一步是启用 API 管理 REST API 并确保其安全性。

 1. 在 API 管理服务中，选择“安全性”下方的“管理 API - 预览”。
 2. 勾选“启用 API 管理 REST API”复选框。
 3. 请注意，管理 API URL 是我们稍后将用于设置 Service Fabric 后端的 URL
 4. 通过选择到期日期和密钥生成“访问令牌”，然后单击页面底部的“生成”按钮。
 5. 复制“访问令牌”并进行保存，以下步骤中将会用到它。 请注意，它与主密钥和辅助密钥不同。

#### <a name="upload-a-service-fabric-client-certificate"></a>上传 Service Fabric 客户端证书

API 管理必须使用有权访问群集的客户端证书对用于服务发现的 Service Fabric 群集进行身份验证。 为简单起见，本教程使用在创建 Service Fabric 群集时指定的相同的证书，该证书默认可用于访问你的群集。

 1. 在 API 管理服务中，选择“安全性”下的“客户端证书 - 预览”。
 2. 单击“+ 添加”按钮
 2. 选择在创建 Service Fabric 群集时指定的群集证书私钥文件 (.pfx)，并为其提供一个名称和私钥密码。

> [!NOTE]
> 本教程对客户端身份验证和群集节点到节点安全性使用相同的证书。 如果你配置了一个单独的客户端证书，则可以使用它来访问 Service Fabric 群集。

### <a name="configure-the-backend"></a>配置后端

在配置了 API 管理安全后，现在可以配置 Service Fabric 后端。 对于 Service Fabric 后端而言，后端是 Service Fabric 群集，而不是特定的 Service Fabric 服务。 这允许单个策略路由到群集中的多个服务。

此步骤需要先前生成的访问令牌，还需要上一步中上传到 API 管理的群集证书指纹。

> [!NOTE]
> 如果将上一步中单独的客户端证书用于 API 管理，那么在此步骤中，除了群集证书指纹之外，你还需要客户端证书指纹。

在启用 API 管理 REST API 时，将以下 HTTP PUT 请求发送到前面记下的 API 管理 API URL，以配置 Service Fabric 后端服务。 命令成功执行后，应该会看到 `HTTP 201 Created` 响应。 有关每个字段的详细信息，请参阅 API 管理[后端 API 参考文档](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)。

HTTP 命令和 URL：
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

请求标头：
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

请求正文：
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

如果后端策略中未指定任何服务名称，那么此处的 url 参数是群集中默认将请求路由到的服务的完全限定的服务名称。 如果你不打算获取回退服务，可以使用一个假的服务名称，如“fabric:/fake/service”。

有关每个字段的详细信息，请参阅 API 管理[后端 API 参考文档](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)。

#### <a name="example"></a>示例

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

## <a name="deploy-a-service-fabric-back-end-service"></a>部署 Service Fabric 后端服务

你已将 Service Fabric 作为后端配置到 API 管理，现在可以为向 Service Fabric 服务发送流量的 API 编写后端策略。 但首先需要在 Service Fabric 中运行的一项服务来接受请求。

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>使用 HTTP 终结点创建 Service Fabric 服务

在本教程中，我们将使用默认的 Web API 项目模板创建一个基本的无状态 ASP.NET Core 可靠服务。 这将为服务创建一个 HTTP 终结点，你将通过 Azure API 管理公开此终结点：

```
/api/values
```

从[为 ASP.NET Core 开发设置开发环境](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core)开始。

开发环境设置完成后，以管理员身份启动 Visual Studio 并创建 ASP.NET Core 服务：

 1. 在 Visual Studio 中，选择“文件”->“新建项目”。
 2. 选择“云”下的 Service Fabric 应用程序模板并将其命名为“ApiApplication”。
 3. 选择 ASP.NET Core 服务模板并将项目命名为“WebApiService”。
 4. 选择 Web API ASP.NET Core 1.1 项目模板。
 5. 创建项目后，打开 `PackageRoot\ServiceManifest.xml` 并从终结点资源配置中删除 `Port` 属性：
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    这允许 Service Fabric 从应用程序端口范围动态指定一个端口，我们通过群集 Resource Manager 模板中的网络安全组将其打开，从而允许流量从 API 管理流向该端口。
 
 6. 可在本地于 Visual Studio 中按下 F5 来验证 Web API。 

    打开 Service Fabric 资源管理器并深入了解 ASP.NET Core 服务的特定实例，以查看服务正在侦听的基址。 将 `/api/values` 添加到基址，并在浏览器中打开它。 这将对 Web API 模板中的 ValuesController 调用 Get 方法。 它会返回模板提供的默认响应，即包含两个字符串的 JSON 数组：

    ```json
    ["value1", "value2"]`
    ```

    这是你将通过 Azure 中的 API 管理公开的终结点。

 7. 最后，将应用程序部署到 Azure 中的群集。 [使用 Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box)，右键单击“应用程序”项目，然后选择“发布”。 提供群集终结点（例如，`mycluster.westus.cloudapp.azure.com:19000`），将应用程序部署到 Azure 中的 Service Fabric 群集。

名为 `fabric:/ApiApplication/WebApiService` 的 ASP.NET Core 无状态服务现在应在 Azure 的 Service Fabric 群集中运行。

## <a name="create-an-api-operation"></a>创建 API 操作

现在我们已准备好在 API 管理中创建一个操作，外部客户端使用该操作与在 Service Fabric 群集中运行的 ASP.NET Core 无状态服务进行通信。

 1. 登录到 Azure 门户并导航到 API 管理服务部署。
 2. 在“API 管理服务”边栏选项卡中，选择“API - 预览”
 3. 通过单击“空白 API”框并填写对话框来添加一个新的 API：

     - **Web 服务 URL**：对于 Service Fabric 后端，不使用此 URL 值。 你可以在此处设置任何值。 在本教程中，使用：`http://servicefabric`。
     - **名称**：为 API 提供任意名称。 在本教程中，使用 `Service Fabric App`。
     - **URL 方案**：选择 HTTP、HTTPS 或同时选择两者。 在本教程中，使用 `both`。
     - **API URL 后缀**：为 API 提供一个后缀。 在本教程中，使用 `myapp`。
 
 4. 创建 API 后，单击“+ 添加操作”添加一个前端 API 操作。 填写以下值：
    
     - **URL**：选择 `GET` 并为 API 提供 URL 路径。 在本教程中，使用 `/api/values`。
     
       默认情况下，在此处指定的 URL 路径是发送到后端 Service Fabric 服务的 URL 路径。 如果在此处使用与服务所使用的相同 URL 路径（此处为 `/api/values`），则无需进一步修改即可正常执行该操作。 还可以在此处指定一个与后端 Service Fabric 服务使用的 URL 路径不同的 URL 路径，这种情况下，还需要在以后的操作策略中指定路径重写。
     - **显示名称**：为 API 提供任意名称。 在本教程中，使用 `Values`。

## <a name="configure-a-backend-policy"></a>配置后端策略

后端策略将所有内容联系在一起。 你可以在其中配置将请求路由到的后端 Service Fabric 服务。 可以将此策略应用到任何 API 操作。 [Service Fabric 的后端配置](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)提供以下请求路由控件： 
 - 服务实例选择，方法是指定硬编码的（例如，`"fabric:/myapp/myservice"`）或从 HTTP 请求中生成的（例如，`"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`）Service Fabric 服务实例名称。
 - 分区解析，方法是使用任何 Service Fabric 分区方案生成分区键。
 - 用于有状态服务的副本选择。
 - 解析重试条件，允许你指定用于重新解析服务位置并重新发送请求的条件。

在本教程中，创建将请求直接路由到以前部署的 ASP.NET Core 无状态服务的后端策略：

 1. 通过单击编辑图标，然后选择“代码视图”，为 `Values` 操作选择和编辑“入站策略”。
 2. 在策略代码编辑器中，在如下所示的入站策略下添加 `set-backend-service` 策略，然后单击“保存”按钮：
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
           sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
      </inbound>
      <backend>
        <base/>
      </backend>
      <outbound>
        <base/>
      </outbound>
    </policies>
    ```

有关完整的 Service Fabric 后端策略属性，请参阅 [API 管理后端文档](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

### <a name="add-the-api-to-a-product"></a>将 API 添加到产品。 

在调用 API 之前，必须将其添加到你可以向用户授予访问权限的产品中。 

 1. 在 API 管理服务中，选择“产品 - 预览”。
 2. 默认情况下，API 管理提供两个产品：Starter 和 Unlimited。 选择 Unlimited 产品。
 3. 选择 API。
 4. 单击“**+ 添加**”按钮。
 5. 选择之前的步骤中创建的 `Service Fabric App` API，然后单击“选择”按钮。

### <a name="test-it"></a>测试

现在可以尝试直接从 Azure 门户通过 API 管理将请求发送到 Service Fabric 中的后端服务。

 1. 在 API 管理服务中，选择“API - 预览”。
 2. 在之前的步骤中创建的 `Service Fabric App` API 中，选择“测试”选项卡。
 3. 单击“发送”按钮，将测试请求发送到后端服务。

## <a name="next-steps"></a>后续步骤

至此，应该完成了 Service Fabric 和 API 管理的基本设置。

本教程使用 Service Fabric 群集的基于证书的基本用户身份验证来帮助你快速设置。 Service Fabric 群集的更高级的用户身份验证更适合使用 [Azure Active Directory 身份验证](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication)。 

接下来，[在 Azure API 管理中创建和配置高级产品设置](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules)，使应用程序准备好进行实际通信。

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png

