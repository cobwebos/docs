---
title: 在 Azure 中将 API 管理与 Service Fabric 集成 |Microsoft Docs
description: 本教程介绍 Azure API 管理和 Service Fabric 的快速入门方法。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1b0588e25c0d156080a2e879185b76714d8691b2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113368"
---
# <a name="tutorial-integrate-api-management-with-service-fabric-in-azure"></a>教程：在 Azure 中将 API 管理与 Service Fabric 集成

本教程是一个系列中的第四部分，  使用 Service Fabric 部署 Azure API 管理是一个高级方案。  当需要使用一组丰富的路由规则为后端 Service Fabric 服务发布 API 时，API 管理非常有用。 云应用程序通常都需要使用前端网关，为用户、设备或其他应用程序提供同一个入口点。 在 Service Fabric 中，网关可以是专为流量入口（如 ASP.NET Core 应用程序、事件中心、IoT 中心或 Azure API 管理）设计的任意无状态服务。

本教程演示如何设置 [Azure API 管理](../api-management/api-management-key-concepts.md)与 Service Fabric，以在 Service Fabric 中将流量路由至后端服务。  完成后，便已将 API 管理部署到 VNET，并已将 API 操作配置为将流量发送到后端无状态服务。 若要详细了解使用 Service Fabric 的 Azure API 管理方案，请参阅[概述](service-fabric-api-management-overview.md)一文。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 部署 API 管理
> * 配置 API 管理
> * 创建 API 操作
> * 配置后端策略
> * 将 API 添加到产品

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 使用模板在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
> * [缩小或扩大群集](service-fabric-tutorial-scale-cluster.md)
> * [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)
> * 部署 API 管理与 Service Fabric

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)或 [Azure CLI 2.0](/cli/azure/install-azure-cli)。
* 在 Azure 上创建安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* 如果部署 Windows 群集，请设置 Windows 开发环境。 安装 [Visual Studio 2017](http://www.visualstudio.com) 和 **Azure 开发**、**ASP.NET 和 Web 开发**以及 **.NET Core 跨平台开发**工作负荷。  然后设置 [.NET 开发环境](service-fabric-get-started.md)。
* 如果部署 Linux 群集，请在 [Linux](service-fabric-get-started-linux.md) 或 [MacOS](service-fabric-get-started-mac.md) 上设置一个 Java 开发环境。  安装 [Service Fabric CLI](service-fabric-cli.md)。

## <a name="network-topology"></a>网络拓扑

既然在 Azure 上拥有安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)，可以在子网和专为 API 管理指定的 NSG 中将 API 管理部署到虚拟网络 (VNET)。 在本教程中，已预配置 API 管理资源管理器模板以使用在前文 [Windows 群集教程](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 群集教程](service-fabric-tutorial-create-vnet-and-linux-cluster.md)中设置的 VNET、子网和 NSG 的名称。 本教程将以下拓扑部署到 Azure，其中 API 管理和 Service Fabric 位于同一虚拟网络的子网中：

 ![图片标题][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>登录到 Azure，然后选择订阅

执行 Azure 命令之前，登录到你的 Azure 帐户并选择你的订阅。

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>部署 Service Fabric 后端服务

在配置 API 管理以将流量路由到 Service Fabric 后端服务前，首先需要一个运行的服务来接受请求。  如果前面创建了 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)，请部署 .NET Service Fabric 服务。  如果前面创建了 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)，请部署 Java Service Fabric 服务。

### <a name="deploy-a-net-service-fabric-service"></a>部署 .NET Service Fabric 服务

在本教程中，使用默认的 Web API 项目模板创建一个基本的无状态 ASP.NET Core 可靠服务。 这可为服务创建一个 HTTP 终结点，可通过 Azure API 管理公开此终结点。

以管理员身份启动 Visual Studio 并创建 ASP.NET Core 服务：

 1. 在 Visual Studio 中，选择“文件”->“新建项目”。
 2. 选择“云”下的 Service Fabric 应用程序模板并将其命名为“ApiApplication”。
 3. 选择无状态 ASP.NET Core 服务模板并将项目命名为“WebApiService”。
 4. 选择 Web API ASP.NET Core 2.0 项目模板。
 5. 创建项目后，打开 `PackageRoot\ServiceManifest.xml` 并从终结点资源配置中删除 `Port` 属性：

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    删除端口允许 Service Fabric 从应用程序端口范围动态指定一个端口，可通过群集资源管理器模板中的网络安全组将其打开，从而允许流量从 API 管理流向该端口。

 6. 可在本地于 Visual Studio 中按下 F5 来验证 Web API。

    打开 Service Fabric Explorer 并深入了解 ASP.NET Core 服务的特定实例，以查看服务正在侦听的基址。 将 `/api/values` 添加到基址，然后在浏览器中打开，这在 Web API 模板的 ValuesController 上调用 Get 方法。 它会返回模板提供的默认响应，即包含两个字符串的 JSON 数组：

    ```json
    ["value1", "value2"]`
    ```

    这是你通过 Azure 中的 API 管理公开的终结点。

 7. 最后，将应用程序部署到 Azure 中的群集。 在 Visual Studio 中，右键单击“应用程序”项目，然后选择“发布”。 提供群集终结点（例如，`mycluster.southcentralus.cloudapp.azure.com:19000`），将应用程序部署到 Azure 中的 Service Fabric 群集。

名为 `fabric:/ApiApplication/WebApiService` 的 ASP.NET Core 无状态服务现在应在 Azure 的 Service Fabric 群集中运行。

### <a name="create-a-java-service-fabric-service"></a>创建 Java Service Fabric 服务

本教程部署一个将消息回显给用户的基本 Web 服务器。 此回显服务器示例应用程序包含用于你的服务的一个 HTTP 终结点，此终结点是通过 Azure API 管理公开的。

1. 克隆 Java 入门示例。

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started/reliable-services-actor-sample
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
   sfctl cluster select --endpoint https://mycluster.southcentralus.cloudapp.azure.com:19080 --pem <full_path_to_pem_on_dev_machine> --no-verify
   ./install.sh
   ```

   一个名为 `fabric:/EchoServerApplication/EchoServerService` 的 Java 无状态服务现在应在 Azure 中的 Service Fabric 群集中运行。

5. 打开浏览器并键入 http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage，应该会看到“[version 1.0]Hello World!!!” 显示。

## <a name="download-and-understand-the-resource-manager-templates"></a>下载资源管理器模板并了解其相关信息

下载并保存以下资源管理器模板和参数文件：

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

*network-apim.json* 模板将新的子网和网络安全组部署在已部署的 Service Fabric 群集所在的虚拟网络中。

以下部分介绍由 apim.json 模板定义的资源。 有关详细信息，请访问每个部分中模板引用文档的链接。 文本稍后部分设置了 apim.parameters.json 参数文件中定义的可配置参数。

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) 描述了 API 管理服务实例：名称、SKU 或层级、资源组位置、发布者信息和虚拟网络。

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) 用于配置 API 管理安全性。 API 管理必须使用有权访问群集的客户端证书对用于服务发现的 Service Fabric 群集进行身份验证。 本教程使用在之前创建 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor)或 [Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor)时指定的相同证书，该证书默认可用于访问群集。

本教程对客户端身份验证和群集节点到节点安全性使用相同的证书。 如果你配置了一个单独的客户端证书，则可以使用它来访问 Service Fabric 群集。 提供创建 Service Fabric 群集时指定的群集证书私钥文件 (.pfx) 的“名称”、“密码”和“数据”（base-64 编码字符串）。

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) 描述了流量转发到的后端服务。

对于 Service Fabric 后端，后端是 Service Fabric 群集，而不是特定的 Service Fabric 服务。 这允许单个策略路由到群集中的多个服务。 如果后端策略中未指定任何服务名称，那么此处的 url 字段是群集中一个服务的完全限定的服务名称，默认情况下所有请求都路由到该服务。 如果你不打算获取回退服务，可以使用一个假的服务名称，如“fabric:/fake/service”。 resourceId 指定群集管理终结点。  clientCertificateThumbprint 和 serverCertificateThumbprints 标识用于对群集进行身份验证的证书。

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) 用于创建产品。 在 Azure API 管理中，产品包含一个或多个 API 以及使用配额和使用条款。 一旦产品发布，开发人员可以订阅该产品，并开始使用产品的 API。

为产品输入描述性“displayName”和“description”。 对于本教程，需要订阅，但不需要管理员的订阅批准。  产品“state”为“已发布”，并对订阅者可见。

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) 用于创建 API。 API 管理中的 API 表示一组可由客户端应用程序调用的操作。 一旦添加操作，该 API 添加到某一产品并可以发布。 发布 API 后，它可供开发人员订阅和使用。

* “displayName”可以是 API 的任意名称。 在本教程中，使用“Service Fabric App”。
* “name”为 API 提供一个唯一且有描述性的名称，例如“service-fabric-app”。 它显示在开发人员和发布者门户中。
* “serviceUrl”引用实现 API 的 HTTP 服务。 API 管理将请求转发到此地址。 对于 Service Fabric 后端，不使用此 URL 值。 你可以在此处设置任何值。 本教程以“http://servicefabric”为例。
* “path”附加到 API 管理服务的基础 URL。 基础 URL 是常见的由 API 管理服务实例托管的所有 API。 API 管理通过其后缀区分 API，因此后缀对给定发布者上的每个 API 必须唯一。
* “protocols”确定可用于访问 API 的协议。 本教程列出“http”和“https”。
* “path”是 API 的后缀。 在本教程中，使用“myapp”。

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) 在使用 API 管理中的 API 前，必须向 API 添加操作。  外部客户端使用操作与 Service Fabric 群集中运行的 ASP.NET Core 无状态服务进行通信。

要添加前端 API 操作，请填写以下值：

* “displayName”和“description”描述操作。 在本教程中，使用“Values”。
* “method”指定 HTTP 谓词。  本教程指定“GET”。
* “urlTemplate”附加到 API 的基础 URL，并标识单个 HTTP 操作。  在本教程中，如果添加了.NET 后端服务，则使用 `/api/values`，如果添加了 Java 后端服务，则使用 `getMessage`。  默认情况下，在此处指定的 URL 路径是发送到后端 Service Fabric 服务的 URL 路径。 如果在此处使用服务所用的相同 URL 路径（例如“/api/values”），则无需进一步修改即可正常执行该操作。 还可以在此处指定一个与后端 Service Fabric 服务使用的 URL 路径不同的 URL 路径，这种情况下，还需要在以后的操作策略中指定路径重写。

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) 创建将所有内容联系在一起的后端策略。 你可以在其中配置将请求路由到的后端 Service Fabric 服务。 可以将此策略应用到任何 API 操作。  有关详细信息，请参阅[策略概述](/azure/api-management/api-management-howto-policies)。

[Service Fabric 的后端配置](/azure/api-management/api-management-transformation-policies#SetBackendService)提供以下请求路由控件：

* 服务实例选择，方法是指定硬编码的（例如，`"fabric:/myapp/myservice"`）或从 HTTP 请求中生成的（例如，`"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`）Service Fabric 服务实例名称。
* 分区解析，方法是使用任何 Service Fabric 分区方案生成分区键。
* 用于有状态服务的副本选择。
* 解析重试条件，允许你指定用于重新解析服务位置并重新发送请求的条件。

“policyContent”是策略的 JSON 转义 XML 内容。  在本教程中，创建将请求直接路由到以前部署的 .NET 或 Java 无状态服务的后端策略。 在入站策略下添加 `set-backend-service` 策略。  如果之前部署了 .NET 后端服务，则将 *sf-service-instance-name* 值替换为 `fabric:/ApiApplication/WebApiService`，如果部署了 Java 服务，则替换为 `fabric:/EchoServerApplication/EchoServerService`。  *backend-id* 引用一个后端资源，在此示例中为在 *apim.json* 模板中定义的 `Microsoft.ApiManagement/service/backends` 资源。 *backend-id* 也可引用使用 API 管理 API 创建的另一后端资源。 对于本教程，请将 *backend-id* 设置为 *service_fabric_backend_name* 参数的值。

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
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

## <a name="set-parameters-and-deploy-api-management"></a>设置参数和部署 API 管理

在部署的 apim.parameters.json 中填写以下空参数。

|参数|值|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|开发人员|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 编码字符串&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;XML 字符串&gt;|

“certificatePassword”和“serviceFabricCertificateThumbprint”必须与用于设置群集的群集证书匹配。

“serviceFabricCertificate”是 base-64 编码字符串形式的证书，可以使用以下脚本生成：

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

在 *inbound_policy* 中，如果之前部署了 .NET 后端服务，则将 *sf-service-instance-name* 值替换为 `fabric:/ApiApplication/WebApiService`，如果部署了 Java 服务，则替换为 `fabric:/EchoServerApplication/EchoServerService`。 *backend-id* 引用一个后端资源，在此示例中为在 *apim.json* 模板中定义的 `Microsoft.ApiManagement/service/backends` 资源。 *backend-id* 也可引用使用 API 管理 API 创建的另一后端资源。 对于本教程，请将 *backend-id* 设置为 *service_fabric_backend_name* 参数的值。

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
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

使用以下脚本为 API 管理部署资源管理器模板和参数文件：

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>测试

现在可以尝试直接从 [Azure 门户](https://portal.azure.com)通过 API 管理将请求发送到 Service Fabric 中的后端服务。

 1. 在 API 管理服务中，选择“API”。
 2. 在于之前的步骤中创建的“Service Fabric 应用”API 中，选择“测试”选项卡，然后选择“值”操作。
 3. 单击“发送”按钮，将测试请求发送到后端服务。  应该会看到类似于以下内容的 HTTP 响应：

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>清理资源

群集由群集资源本身以及其他 Azure 资源组成。 若要删除群集及其占用的所有资源，最简单的方式是删除资源组。

登录到 Azure，选择要删除的群集的订阅 ID。  可通过登录到 [Azure 门户](http://portal.azure.com)查找订阅 ID。 使用 [Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组和所有群集资源。

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：

> [!div class="checklist"]
> * 部署 API 管理
> * 配置 API 管理
> * 创建 API 操作
> * 配置后端策略
> * 将 API 添加到产品

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
