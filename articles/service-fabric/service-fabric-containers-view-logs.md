---
title: 查看 Azure Service Fabric 中的容器日志 | Microsoft Docs
description: 介绍在使用 Service Fabric Explorer 运行 Service Fabric 容器服务时，如何查看相应的容器日志。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: ryanwi
ms.openlocfilehash: c8b6bc791700e6811f5681ee70329e4d2ac05991
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824605"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>查看 Service Fabric 容器服务的日志
Azure Service Fabric 是一种容器业务流程协调程序，支持 [Linux 和 Windows 容器](service-fabric-containers-overview.md)。  本文介绍如何查看正在运行的容器服务或不活动容器的容器日志，以便诊断和排查问题。

## <a name="access-the-logs-of-a-running-container"></a>访问正在运行的容器的日志
可以通过 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 来访问容器日志。  在 Web 浏览器中导航到 [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer)，以便从群集的管理终结点打开 Service Fabric Explorer。  

容器日志位于容器服务实例运行时所在的群集节点。 例如，可以获取 [Linux Voting 示例应用程序](service-fabric-quickstart-containers-linux.md)的 Web 前端容器的日志。 在树状视图中，展开“群集”>“应用程序”>“VotingType”>“fabric:/Voting/azurevotefront”。  然后展开分区（在此示例中为 d1aa737e-f22a-e347-be16-eec90be24bc1），此时会看到容器在群集节点 *_lnxvm_0* 上运行。

在树状视图中找到 *_lnxvm_0* 节点上的代码包，方法是展开“节点”>“_lnxvm_0”>“fabric:/Voting”>“azurevotfrontPkg”>“代码包”>“代码”。  然后选择“容器日志”选项，以便显示容器日志。

![Service Fabric 平台][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>访问不活动或故障容器的日志
从 v6.2 开始，还可以使用 [REST API](/rest/api/servicefabric/sfclient-index) 或 [Service Fabric CLI (SFCTL)](service-fabric-cli.md) 命令提取不活动或故障容器的日志。

### <a name="set-container-retention-policy"></a>设置容器保留策略
为了帮助诊断容器启动故障，Service Fabric（6.1 或更高版本）支持保留终止的或无法启动的容器。 此策略可以在 ApplicationManifest.xml 文件中设置，如以下代码片段所示：
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

ContainersRetentionCount 设置指定在容器故障时需保留的容器数。 如果指定一个负值，则会保留所有故障容器。 如果不指定 ContainersRetentionCount 属性，则不会保留任何容器。 ContainersRetentionCount 属性还支持应用程序参数，因此用户可以为测试性群集和生产群集指定不同的值。 使用此功能时可使用放置约束，将容器服务的目标设置为特定的节点，防止将容器服务移至其他节点。 使用此功能保留的容器必须手动删除。

### <a name="rest"></a>REST
使用[获取部署在节点上的容器日志](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode)操作，来获取故障容器的日志。 指定运行该容器的节点名称、应用程序名称、服务清单名称和代码包名称。  指定 `&Previous=true`。 该响应将包含该代码包实例中不活动容器的容器日志。

请求 URI 的格式如下：

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

示例请求：
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 响应正文：
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
使用 [sfctl service get-container-logs](service-fabric-sfctl-service.md) 命令来提取故障容器的日志。  指定运行该容器的节点名称、应用程序名称、服务清单名称和代码包名称。 指定 `-previous` 标志。  该响应将包含该代码包实例中不活动容器的容器日志。

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
响应：
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>后续步骤
- 详细了解[创建 Linux 容器应用程序教程](service-fabric-tutorial-create-container-images.md)。
- 详细了解 [Service Fabric 和容器](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
