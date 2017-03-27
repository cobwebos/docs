---
title: "Azure Service Fabric 映像存储连接字符串 | Microsoft Docs"
description: "了解映像存储连接字符串"
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: 
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/08/2017
ms.author: alexwun
translationtype: Human Translation
ms.sourcegitcommit: be4275d55410a763d38dcb954df5349db287c015
ms.openlocfilehash: 5eb8f9489dcc29122892165111f62d63be1766f2


---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>了解 ImageStoreConnectionString 设置

在某些文档中，浅要提及了“ImageStoreConnectionString”参数的存在，但未阐述其真正的含义。 阅读诸如[使用 PowerShell 部署和删除应用程序][10]等文章后，貌似你只需在目标群集的群集清单中出现值时复制/粘贴该值。 因此设置必须可按群集进行配置，但在通过 [Azure 门户][11]创建群集时，无法配置此设置且其始终为“fabric:ImageStore”。 那么，此设置有何用途？

![群集清单][img_cm]

Service Fabric 起初是一个供许多不同团队在内部 Microsoft 使用的平台，因此其某些方面可高度自定义（“映像存储区”就是此类方面）。 从根本上来说，映像存储区是用于存储应用程序包的可插入存储库。 将应用程序部署到群集中的节点时，该节点将从映像存储区下载应用程序包的内容。 ImageStoreConnectionString 是一种设置，其中包括查找适合给定群集的映像存储区时所需的所有客户端和节点信息。

目前有&3; 种可能类型的映像存储区提供程序，其相应的连接字符串如下所示：

1. 映像存储区服务：“fabric: ImageStore”

2. 文件系统：“file:[file system path]”

3. Azure 存储：“xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]”

生产中使用的提供程序类型是映像存储区服务，这是一种可从 Service Fabric Explorer 查看的状态持久化系统服务。 

![映像存储区服务][img_is]

通过在群集自身的系统服务中承载映像存储区，可消除程序包存储库的外部依赖关系，并可更大程度控制存储区域。 围绕映像存储区的后续改进很可能先针对映像存储区提供程序（若非唯一目标）。 由于客户端已连接到目标群集，因此映像存储区服务提供程序的连接字符串不具有任何唯一信息。 客户端只需知道应使用哪些面向系统服务的协议。

开发期间，为了略微加快群集的启动，将对本地单机群集使用文件系统提供程序，而不是映像存储区服务。 区别通常很小，但对大多数人而言，这是开发期间的一项实用优化。 也可通过其他存储提供程序类型部署本地单机群集，但通常无需这样做，因为不管提供程序如何，开发/测试工作流将保持不变。 除此类用法外，文件系统和 Azure 存储提供程序仅用于支持旧版。

因此虽然可配置 ImageStoreConnectionString，但通常只需使用默认设置。 通过 [Visual Studio][12] 发布到 Azure 时，该参数会相应地自动设置。 对于 Azure 中托管的群集的编程部署，连接字符串始终为“fabric: ImageStore”。 有疑问时，始终可通过 [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest)、[.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) 或 [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest) 检索群集清单验证其值。 同样，本地测试和生产群集应始终配置为使用映像存储区服务提供程序。

### <a name="next-steps"></a>后续步骤
[使用 PowerShell 部署和删除应用程序][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-publish-app-remote-cluster.md



<!--HONumber=Feb17_HO2-->


