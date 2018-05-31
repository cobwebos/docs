---
title: Azure Service Fabric DNS 服务 | Microsoft Docs
description: 使用 Service Fabric 的 DNS 服务从群集内部发现微服务。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204988"
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric 中的 DNS 服务
DNS 服务是可选的系统服务，可以在群集中启用，用于发现使用 DNS 协议的其他服务。 

许多服务（特别是容器化服务）可以拥有一个现有的 URL 名称，因此能够使用标准 DNS 协议（而不是命名服务协议）解析这些名称就很有必要，尤其是在应用程序“提升和转移”方案中。 借助 DNS 服务，可将 DNS 名称映射到服务名称，进而解析终结点 IP 地址。 

DNS 服务将 DNS 名称映射到服务名称，命名服务将服务名称进行解析并将其发送回服务终结点。 在创建时提供服务的 DNS 名称。

![服务终结点](./media/service-fabric-dnsservice/dns.png)

DNS 服务不支持动态端口。 若要解析动态端口上公开的服务，请使用[反向代理服务](./service-fabric-reverseproxy.md)。

## <a name="enabling-the-dns-service"></a>启用 DNS 服务
使用门户创建群集时，默认情况下，在“群集配置”菜单的“包括 DNS 服务”复选框中启用 DNS 服务：

![通过门户启用 DNS 服务][2]

如果不使用门户创建群集或者要更新现有群集，则需要在模板中启用 DNS 服务：

- 若要部署新的群集，可以使用[示例模板](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)或创建自己的资源管理器模板。 
- 若要更新现有群集，可以导航到门户的群集资源组并单击“自动化脚本”，使用反映群集和组中其他资源当前状态的模板。 若要了解详细信息，请参阅[从资源组导出模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group)。

有了模板后，可以通过以下步骤启用 DNS 服务：

1. 检查 `apiversion` 是否针对 `Microsoft.ServiceFabric/clusters` 设置为 `2017-07-01-preview` 或更高版本，如果没有，请按以下代码片段所示进行更新：

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

3. 通过前述更改更新群集模板后，应用更改并等待升级完成。 完成升级后，DNS 系统服务将开始在群集中运行。 服务名称是 `fabric:/System/DnsService`，可以在 Service Fabric Explorer 的“系统”服务部分下找到它。 


## <a name="setting-the-dns-name-for-your-service"></a>设置服务的 DNS 名称
DNS 服务在群集中运行后，可使用声明方式在 `ApplicationManifest.xml` 中为默认服务设置 DNS 名称，或通过 PowerShell 命令设置服务的 DNS 名称。

可以在整个群集中解析服务的 DNS 名称。 强烈建议使用 `<ServiceDnsName>.<AppInstanceName>` 命名方案；例如 `service1.application1`。 这样做可以确保 DNS 名称在整个群集中的唯一性。 如果使用 Docker Compose 部署应用程序，服务会自动分配使用此命名方案的 DNS 名称。

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
如果部署多个服务，则可使用 DNS 名称找到用于通信的其他服务的终结点。 DNS 服务仅适用于无状态服务，因为 DNS 协议无法与有状态服务通信。 对于有状态服务，可使用 HTTP 调用的内置[反向代理服务](./service-fabric-reverseproxy.md)调用特定服务分区。 DNS 服务不支持动态端口。 反向代理可用于解析使用动态端口的服务。

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
