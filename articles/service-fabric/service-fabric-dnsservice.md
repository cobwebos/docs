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
ms.date: 7/20/2018
ms.author: msfussell
ms.openlocfilehash: 4d248724597a411f7253be1ccca0be6b85db95af
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237173"
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric 中的 DNS 服务
DNS 服务是可选的系统服务，可以在群集中启用，用于发现使用 DNS 协议的其他服务。 

许多服务（特别是容器化服务）可以通过现存的 URL 来寻址。 能够使用标准 DNS 协议（而不是 Service Fabric 命名服务协议）解析这些名称是很有必要的。 借助 DNS 服务，可将 DNS 名称映射到服务名称，进而解析终结点 IP 地址。 此类功能可在不同的平台之间保持容器化服务的可移植性，并更方便地利用“直接迁移”方案，因为它可以让你使用现有的服务 URL，而无需重新编写代码来利用命名服务。 

DNS 服务将 DNS 名称映射到服务名称，命名服务将服务名称进行解析并将其发送回服务终结点。 在创建时提供服务的 DNS 名称。 下图显示了如何对无状态服务运行 DNS 服务。

![服务终结点](./media/service-fabric-dnsservice/stateless-dns.png)

从 Service Fabric 版本 6.3 开始，Service Fabric DNS 协议经过扩展，现在包含用于寻址已分区的有状态服务的方案。 使用这些扩展可以通过有状态服务 DNS 名称和分区名称的组合来解析特定的分区 IP 地址。 支持所有三种分区方案：

- 命名分区
- 按范围分区
- 单一实例分区

下图显示了如何分区的有状态服务运行 DNS 服务。

![有状态服务终结点](./media/service-fabric-dnsservice/stateful-dns.png)

DNS 服务不支持动态端口。 若要解析动态端口上公开的服务，请使用[反向代理服务](./service-fabric-reverseproxy.md)。

## <a name="enabling-the-dns-service"></a>启用 DNS 服务
使用门户创建群集时，默认情况下，在“群集配置”菜单的“包括 DNS 服务”复选框中启用 DNS 服务：

![通过门户启用 DNS 服务](./media/service-fabric-dnsservice/enable-dns-service.png)

如果不使用门户创建群集或者要更新现有群集，则需要在模板中启用 DNS 服务：

- 若要部署新的群集，可以使用[示例模板](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)或创建自己的资源管理器模板。 
- 若要更新现有群集，可以导航到门户的群集资源组并单击“自动化脚本”，使用反映群集和组中其他资源当前状态的模板。 若要了解详细信息，请参阅[从资源组导出模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group)。

有了模板后，可以通过以下步骤启用 DNS 服务：

1. 检查 `Microsoft.ServiceFabric/clusters` 资源的 `apiversion` 是否设置为 `2017-07-01-preview` 或更高，如果不是，请按以下示例所示进行更新：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 现在，通过以下方式之一启用 DNS 服务：

   - 若要启用采用默认设置的 DNS 服务，请将其添加到 `properties` 节中的 `addonFeatures` 节，如以下示例所示：

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - 若要启用采用非默认设置的服务，请将 `DnsService` 节添加到 `properties` 节中的 `fabricSettings` 节。 在这种情况下，不需要将 DnsService 添加到 `addonFeatures`。 若要详细了解可为 DNS 服务设置的属性，请参阅 [DNS 服务设置](./service-fabric-cluster-fabric-settings.md#dnsservice)。

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. 通过这些更改更新群集模板后，请应用更改并等待升级完成。 完成升级后，DNS 系统服务将开始在群集中运行。 服务名称是 `fabric:/System/DnsService`，可以在 Service Fabric Explorer 的“系统”服务部分下找到它。 


## <a name="setting-the-dns-name-for-your-service"></a>设置服务的 DNS 名称
可以在 ApplicationManifest.xml 文件中或者通过 PowerShell 命令，以声明方式为默认服务设置 DNS 名称。

服务的 DNS 名称可在整个群集中解析，因此，请务必确保 DNS 名称在整个群集中的唯一性。 

强烈建议使用 `<ServiceDnsName>.<AppInstanceName>` 命名方案；例如 `service1.application1`。 如果使用 Docker Compose 部署应用程序，服务会自动分配使用此命名方案的 DNS 名称。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>在 ApplicationManifest.xml 中为默认服务设置 DNS 名称
在 Visual Studio 中或所选的编辑器中打开项目，并打开 ApplicationManifest.xml 文件。 转到默认服务部分，为每个服务添加 `ServiceDnsName` 属性。 以下示例说明如何将服务的 DNS 名称设置为 `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
部署应用程序后，Service Fabric Explorer 中的服务实例会显示此实例的 DNS 名称，如下图所示： 

![服务终结点](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

以下示例将有状态服务的 DNS 名称设置为 `statefulsvc.app`。 该服务使用命名分区方案。 请注意分区名称均为小写。 这是在 DNS 查询中用作目标的分区的一项要求；有关详细信息，请参阅[针对有状态服务分区发出 DNS 查询](#making-dns-queries-on-a-stateful-service-partition)。

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>使用 Powershell 设置服务的 DNS 名称
创建服务时，可以使用 `New-ServiceFabricService` Powershell 命令设置服务的 DNS 名称。 以下示例创建一个 DNS 名称为 `service1.application1` 的新的无状态服务

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

## <a name="making-dns-queries-on-a-stateful-service-partition"></a>针对有状态服务分区发出 DNS 查询
从 Service Fabric 版本 6.3 开始，Service Fabric DNS 服务支持针对服务分区的查询。

对于 DNS 查询中使用的分区，适用以下命名限制：

   - 分区名称应符合 DNS 规范。
   - 不应使用多标签分区名称（名称中包括句点“.”）。
   - 分区名称应为小写。

针对分区的 DNS 查询的格式如下：

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
其中：

- *First-Label-Of-Partitioned-Service-DNSName* 是服务 DNS 名称的第一个部分。
- *PartitionPrefix* 是可以在群集清单的 DnsService 节中设置的，或者通过群集资源管理器模板设置的值。 默认值为“-”。 有关详细信息，请参阅 [DNS 服务设置](./service-fabric-cluster-fabric-settings.md#dnsservice)。
- *Target-Partition-Name* 是分区的名称。 
- *PartitionSuffix* 是可以在群集清单的 DnsService 节中设置的，或者通过群集资源管理器模板设置的值。 默认值为空字符串。 有关详细信息，请参阅 [DNS 服务设置](./service-fabric-cluster-fabric-settings.md#dnsservice)。
- *Remaining-Partitioned-Service-DNSName* 是服务 DNS 名称的剩余部分。

以下示例显示了针对某个群集（该群集对 `PartitionPrefix` 和 `PartitionSuffix` 采用默认设置）上运行的分区服务发出的 DNS 查询： 

- 若要解析具有 DNS 名称 `backendrangedschemesvc.application`（使用按范围分区方案）的服务的分区“0”，请使用 `backendrangedschemesvc-0.application`。
- 若要解析具有 DNS 名称 `backendnamedschemesvc.application`（使用命名分区方案）的服务的分区“first”，请使用 `backendnamedschemesvc-first.application`。

DNS 服务将返回该分区的主要副本的 IP 地址。 如果未指定分区，服务将返回随机选择的分区的主要副本的 IP 地址。

## <a name="using-dns-in-your-services"></a>在服务中使用 DNS
如果部署多个服务，可以使用 DNS 名称找到用于通信的其他服务的终结点。 DNS 服务适用于无状态服务，在 Service Fabric 版本 6.3 和更高版本中，它也适用于有状态服务。 对于运行低于 Service Fabric 6.3 版本的 有状态服务，可以使用 HTTP 调用的内置[反向代理服务](./service-fabric-reverseproxy.md)调用特定的服务分区。 

DNS 服务不支持动态端口。 可以通过反向代理服务解析使用动态端口的服务。

以下代码演示如何通过 DNS 调用无状态服务。 它只是一个普通的 http 调用，其中提供了 DNS 名称、端口和任一可选路径作为 URL 的一部分。

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

以下代码演示如何调用有状态服务的特定分区。 在本例中，DNS 名称包含分区名称 (partition1)。 该调用假设群集对`PartitionPrefix` 和 `PartitionSuffix` 使用默认值。

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
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
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

