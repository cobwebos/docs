---
title: "Azure Service Fabric DNS 服务 | Microsoft Docs"
description: "使用 Service Fabric 的 DNS 服务从群集内部发现微服务。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 9871bc5aa4e74ab0faef401d67c4e9558eb5e14b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric 中的 DNS 服务
DNS 服务是可选的系统服务，可以在群集中启用，用于发现使用 DNS 协议的其他服务。

许多服务（特别是容器化服务）可以拥有一个现有的 URL 名称，因此能够使用标准 DNS 协议（而不是命名服务协议）解析这些名称就很有必要，尤其是在应用程序“提升和转移”方案中。 借助 DNS 服务，可将 DNS 名称映射到服务名称，进而解析终结点 IP 地址。 

DNS 服务将 DNS 名称映射到服务名称，命名服务将服务名称进行解析并将其发送回服务终结点。 在创建时提供服务的 DNS 名称。 

![服务终结点][0]

## <a name="enabling-the-dns-service"></a>启用 DNS 服务
首先，需要在群集中启用 DNS 服务。 获取要部署的群集的模板。 可以使用[示例模板](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)，也可以创建 Resource Manager 模板。 可通过以下步骤启用 DNS 服务：

1. 检查 `apiversion` 是否针对 `Microsoft.ServiceFabric/clusters` 资源设置为 `2017-07-01-preview`，如果没有，请按以下代码片段所示进行更新：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 现在，通过在 `fabricSettings` 部分后添加以下 `addonFeatures` 部分启用 DNS 服务，如以下代码片段所示： 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. 通过前述更改更新群集模板后，应用更改并等待升级完成。 完成后，DNS 系统服务开始在群集中运行，该服务在 Service Fabric Explorer 中的系统服务部分下称为 `fabric:/System/DnsService`。 

或者，可在创建群集时通过门户启用 DNS 服务。 可通过在 `Cluster configuration` 菜单中选中 `Include DNS service` 的框来启用 DNS 服务，如以下屏幕截图所示：

![通过门户启用 DNS 服务][2]


## <a name="setting-the-dns-name-for-your-service"></a>设置服务的 DNS 名称
DNS 服务在群集中运行后，可使用声明方式在 `ApplicationManifest.xml` 中为默认服务设置 DNS 名称，或通过 Powershell 命令设置服务的 DNS 名称。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>在 ApplicationManifest.xml 中为默认服务设置 DNS 名称
在 Visual Studio 中或选择的编辑器中打开项目，并打开 `ApplicationManifest.xml` 文件。 转到默认服务部分，为每个服务添加 `ServiceDnsName` 属性。 以下示例说明如何将服务的 DNS 名称设置为 `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
部署应用程序后，Service Fabric Explorer 中的服务实例会显示此实例的 DNS 名称，如下图所示： 

![服务终结点][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>使用 Powershell 设置服务的 DNS 名称
创建服务时，可以使用 `New-ServiceFabricService` Powershell 设置服务的 DNS 名称。 以下示例创建一个 DNS 名称为 `service1.application1` 的新的无状态服务

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>在服务中使用 DNS
如果部署多个服务，则可使用 DNS 名称找到用于通信的其他服务的终结点。 DNS 服务仅适用于无状态服务，因为 DNS 协议无法与有状态服务通信。 对于有状态服务，可使用 HTTP 调用的内置反向代理调用特定服务分区。

以下代码显示如何调用其他服务，这只是一个常规 HTTP 调用，需提供端口和任意可选路径作为 URL 的一部分。

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>后续步骤
通过[连接服务并与服务进行通信](service-fabric-connect-and-communicate-with-services.md)，了解有关群集内服务通信的详细信息

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
