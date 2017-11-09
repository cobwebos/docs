---
title: "将 Azure Service Fabric 与 API 管理集成 | Microsoft Docs"
description: "了解如何快速开始使用 Azure API 管理和 Service Fabric。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: 8ff8c425189efdd7ea21984528bf7ea765e17955
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>部署 API 管理与 Service Fabric
本教程是一个系列中的第二部分。 本教程演示如何设置 [Azure API 管理](../api-management/api-management-key-concepts.md)与 Service Fabric，以在 Service Fabric 中将流量路由至后端服务。  完成后，便已将 API 管理部署到 VNET，并已将 API 操作配置为将流量发送到后端无状态服务。 若要详细了解使用 Service Fabric 的 Azure API 管理方案，请参阅[概述](service-fabric-api-management-overview.md)一文。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 部署 API 管理
> * 配置 API 管理
> * 创建 API 操作
> * 配置后端策略
> * 将 API 添加到产品

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 使用模板在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
> * 部署 API 管理与 Service Fabric

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)或 [Azure CLI 2.0](/cli/azure/install-azure-cli)。
- 在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
- 如果部署 Windows 群集，请设置 Windows 开发环境。 安装 [Visual Studio 2017](http://www.visualstudio.com) 和 **Azure 开发**、**ASP.NET 和 Web 开发**以及 **.NET Core 跨平台开发**工作负荷。  然后设置 [.NET 开发环境](service-fabric-get-started.md)。
- 如果部署 Linux 群集，请在 [Linux](service-fabric-get-started-linux.md) 或 [MacOS](service-fabric-get-started-mac.md) 上设置一个 Java 开发环境。  安装 [Service Fabric CLI](service-fabric-cli.md)。 

## <a name="sign-in-to-azure-and-select-your-subscription"></a>登录到 Azure，然后选择订阅
执行 Azure 命令之前，登录到你的 Azure 帐户并选择你的订阅。

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>部署 API 管理
云应用程序通常都需要使用前端网关，为用户、设备或其他应用程序提供同一个入口点。 在 Service Fabric 中，网关可以是任意无状态服务（如 ASP.NET Core 应用程序），也可以是其他专为流量入口设计的服务（如事件中心、IoT 中心或 Azure API 管理）。 本教程介绍了如何将 Azure API 管理用作 Service Fabric 应用程序的网关。 API 管理直接与 Service Fabric 集成，以便可以使用一组丰富的路由规则向后端 Service Fabric 服务发布 API。 

既然在 Azure 上拥有安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)，可以在子网和专为 API 管理指定的 NSG 中将 API 管理部署到虚拟网络 (VNET)。 在本教程中，已预配置 API 管理资源管理器模板以使用在前文 [Windows 群集教程](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集教程](service-fabric-tutorial-create-vnet-and-linux-cluster.md)中设置的 VNET、子网和 NSG 的名称。 

下载以下 Resource Manager 模板和参数文件：
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

针对部署填充 `apim.parameters.json` 中的空参数。

使用以下脚本为 API 管理部署资源管理器模板和参数文件：

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>配置 API 管理

在部署 API 管理和 Service Fabric 群集后，即可在 API 管理中配置安全设置和 Service Fabric 后端。 这样，你就可以创建后端服务策略，将流量发送到 Service Fabric 群集。

### <a name="configure-api-management-security"></a>配置 API 管理安全性

要配置 Service Fabric 后端，首先需要配置 API 管理安全性设置。 要配置安全性设置，请转到 Azure 门户中的 API 管理服务。

#### <a name="enable-the-api-management-rest-api"></a>启用 API 管理 REST API

API 管理 REST API 是目前配置后端服务的唯一方法。 第一步是启用 API 管理 REST API 并确保其安全性。

 1. 在 API 管理服务中，选择“安全性”下的“管理 API”。
 2. 勾选“启用 API 管理 REST API”复选框。
 3. 请注意，**管理 API URL** 稍后将用于设置 Service Fabric 后端。
 4. 通过选择到期日期和密钥生成“访问令牌”，然后单击页面底部的“生成”按钮。
 5. 复制“访问令牌”并保存。  在下面的步骤中我们将使用此访问令牌。 请注意，它与主密钥和辅助密钥不同。

#### <a name="upload-a-service-fabric-client-certificate"></a>上传 Service Fabric 客户端证书

API 管理必须使用有权访问群集的客户端证书对用于服务发现的 Service Fabric 群集进行身份验证。 为简单起见，本教程使用在之前创建 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor)时指定的相同证书，该证书默认可用于访问你的群集。

 1. 在 API 管理服务中，选择“安全性”下的“客户端证书”。
 2. 单击“**+ 添加**”按钮。
 2. 选择在创建 Service Fabric 群集时指定的群集证书私钥文件 (.pfx)，并为其提供一个名称和私钥密码。

> [!NOTE]
> 本教程对客户端身份验证和群集节点到节点安全性使用相同的证书。 如果你配置了一个单独的客户端证书，则可以使用它来访问 Service Fabric 群集。

### <a name="configure-the-backend"></a>配置后端

在配置了 API 管理安全后，现在可以配置 Service Fabric 后端。 对于 Service Fabric 后端而言，后端是 Service Fabric 群集，而不是特定的 Service Fabric 服务。 这允许单个策略路由到群集中的多个服务。

此步骤需要先前生成的访问令牌，还需要先前上传到 API 管理的群集证书指纹。

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

如果后端策略中未指定任何服务名称，那么此处的 **url** 参数是群集中默认将请求路由到的服务的完全限定的服务名称。 如果你不打算获取回退服务，可以使用一个假的服务名称，如“fabric:/fake/service”。

有关每个字段的详细信息，请参阅 API 管理[后端 API 参考文档](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)。

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>部署 Service Fabric 后端服务

你已将 Service Fabric 作为后端配置到 API 管理，现在可以为向 Service Fabric 服务发送流量的 API 编写后端策略。 但首先需要在 Service Fabric 中运行的一项服务来接受请求。  如果前面创建了 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)，请部署 .NET Service Fabric 服务。  如果前面创建了 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)，请部署 Java Service Fabric 服务。

### <a name="deploy-a-net-service-fabric-service"></a>部署 .NET Service Fabric 服务

在本教程中，我们将使用默认的 Web API 项目模板创建一个基本的无状态 ASP.NET Core 可靠服务。 这将为服务创建一个 HTTP 终结点，将通过 Azure API 管理公开此终结点：

```
/api/values
```

以管理员身份启动 Visual Studio 并创建 ASP.NET Core 服务：

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

    这是你通过 Azure 中的 API 管理公开的终结点。

 7. 最后，将应用程序部署到 Azure 中的群集。 [使用 Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box)，右键单击“应用程序”项目，然后选择“发布”。 提供群集终结点（例如，`mycluster.southcentralus.cloudapp.azure.com:19000`），将应用程序部署到 Azure 中的 Service Fabric 群集。

名为 `fabric:/ApiApplication/WebApiService` 的 ASP.NET Core 无状态服务现在应在 Azure 的 Service Fabric 群集中运行。

### <a name="create-a-java-service-fabric-service"></a>创建 Java Service Fabric 服务
对于本教程，我们将部署一个将消息回显给用户的基本 web 服务器。 此回显服务器示例应用程序包含用于你的服务的一个 HTTP 终结点，此终结点是通过 Azure API 管理公开的。

1. 克隆 Java 入门示例。

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. 编辑 *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*。 更新终结点，使服务在端口 8081 上进行侦听。

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. 保存 *ServiceManifest.xml*，然后构建 EchoServer1.0 应用程序。

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. 将应用程序部署到群集。

   ```bash
   cd Scripts
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   一个名为 `fabric:/EchoServerApplication/EchoServerService` 的 Java 无状态服务现在应在 Azure 中的 Service Fabric 群集中运行。

5. 打开浏览器并键入 http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage，应该会看到“[version 1.0]Hello World !!!” 显示。


## <a name="create-an-api-operation"></a>创建 API 操作

现在我们已准备好在 API 管理中创建一个操作，外部客户端使用该操作与在 Service Fabric 群集中运行的 ASP.NET Core 无状态服务进行通信。

1. 登录到 Azure 门户并导航到 API 管理服务部署。
2. 在“API 管理服务”边栏选项卡中，选择“API”
3. 通过单击“+API”，单击“空白 API”框，并填写对话框来添加一个新的 API：

    - **Web 服务 URL**：对于 Service Fabric 后端，不使用此 URL 值。 你可以在此处设置任何值。 在本教程中，使用“http://servicefabric”。
    - **显示名称**：为 API 提供任意名称。 在本教程中，使用“Service Fabric App”。
    - **名称**：输入“service-fabric-app”。
    - **URL 方案**：选择 **HTTP**、**HTTPS** 或**同时选择两者**。 在本教程中，**同时使用两者**。
    - **API URL 后缀**：为 API 提供一个后缀。 在本教程中，使用“myapp”。
 
4. 从 API 列表中选择“Service Fabric App”，然后单击“+ 添加操作”以添加前端 API 操作。 填写以下值：
    
    - **URL**：选择“GET”并为 API 提供 URL 路径。 在本教程中，使用“/api/values”。  默认情况下，在此处指定的 URL 路径是发送到后端 Service Fabric 服务的 URL 路径。 如果在此处使用与服务所使用的相同 URL 路径（此处为“/api/values”），则无需进一步修改即可正常执行该操作。 还可以在此处指定一个与后端 Service Fabric 服务使用的 URL 路径不同的 URL 路径，这种情况下，还需要在以后的操作策略中指定路径重写。
    - **显示名称**：为 API 提供任意名称。 在本教程中，使用“Values”。

5. 单击“保存” 。

## <a name="configure-a-backend-policy"></a>配置后端策略

后端策略将所有内容联系在一起。 你可以在其中配置将请求路由到的后端 Service Fabric 服务。 可以将此策略应用到任何 API 操作。 [Service Fabric 的后端配置](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)提供以下请求路由控件： 
 - 服务实例选择，方法是指定硬编码的（例如，`"fabric:/myapp/myservice"`）或从 HTTP 请求中生成的（例如，`"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`）Service Fabric 服务实例名称。
 - 分区解析，方法是使用任何 Service Fabric 分区方案生成分区键。
 - 用于有状态服务的副本选择。
 - 解析重试条件，允许你指定用于重新解析服务位置并重新发送请求的条件。

在本教程中，创建将请求直接路由到以前部署的 ASP.NET Core 无状态服务的后端策略：

 1. 通过单击编辑图标，然后选择“代码视图”，为 Values 操作选择和编辑“入站策略”。
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

### <a name="add-the-api-to-a-product"></a>将 API 添加到产品 

在调用 API 之前，必须将其添加到你可以向用户授予访问权限的产品中。 

 1. 在 API 管理服务中，选择“产品”。
 2. 默认情况下，API 管理提供两个产品：Starter 和 Unlimited。 选择 Unlimited 产品。
 3. 选择“+添加 API”。
 4. 选择之前的步骤中创建的 `Service Fabric App` API，然后单击“选择”按钮。

### <a name="test-it"></a>测试

现在可以尝试直接从 Azure 门户通过 API 管理将请求发送到 Service Fabric 中的后端服务。

 1. 在 API 管理服务中，选择“API”。
 2. 在于之前的步骤中创建的“Service Fabric App”API 中，选择“测试”选项卡，然后选择“Values”操作。
 3. 单击“发送”按钮，将测试请求发送到后端服务。  应该会看到类似于以下内容的 HTTP 响应：

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>清理资源

群集由群集资源本身以及其他 Azure 资源组成。 若要删除群集及其占用的所有资源，最简单的方式是删除资源组。

登录到 Azure，选择要删除的群集的订阅 ID。  可通过登录到 [Azure 门户](http://portal.azure.com)查找订阅 ID。 使用 [Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组和所有群集资源。

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="conclusion"></a>结束语
本教程介绍了如何：

> [!div class="checklist"]
> * 部署 API 管理
> * 配置 API 管理
> * 创建 API 操作
> * 配置后端策略
> * 将 API 添加到产品

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json