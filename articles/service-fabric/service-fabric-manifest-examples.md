---
title: Azure Service Fabric 容器应用程序清单示例 | Microsoft Docs
description: 了解如何为 Service Fabric 应用程序配置应用程序和服务清单设置。
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 10419240f730a6b68f1161f158f7f903a98a9933
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035603"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric 应用程序和服务清单示例
本部分包含应用程序和服务清单的示例。 这些示例并非用来展示重要方案，而是用来展示可用的各种设置以及如何使用它们。 

下面是所显示的功能以及它们所属的示例清单的索引。

|功能|清单|
|---|---|
|[资源调控](service-fabric-resource-governance.md)|[Reliable Services 应用程序清单](service-fabric-manifest-example-reliable-services-app.md#application-manifest)、[容器应用程序清单](service-fabric-manifest-example-container-app.md#application-manifest)|
|[以本地管理帐户身份运行服务](service-fabric-application-runas-security.md)|[Reliable Services 应用程序清单](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[将默认策略应用于所有服务代码包](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services 应用程序清单](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[创建用户和组主体](service-fabric-application-runas-security.md)|[Reliable Services 应用程序清单](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|在服务实例之间共享数据包|[Reliable Services 应用程序清单](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[替代服务终结点](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services 应用程序清单](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[在服务启动时运行脚本](service-fabric-run-script-at-service-startup.md)|[VotingWeb 服务清单](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[定义 HTTPS 终结点](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb 服务清单](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[声明配置包](service-fabric-application-and-service-manifests.md)|[VotingData 服务清单](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[声明数据包](service-fabric-application-and-service-manifests.md)|[VotingData 服务清单](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[替代环境变量](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[容器应用程序清单](service-fabric-manifest-example-container-app.md#application-manifest)|
|[配置容器端口到主机映射](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [容器应用程序清单](service-fabric-manifest-example-container-app.md#application-manifest)|
|[配置容器注册表身份验证](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[容器应用程序清单](service-fabric-manifest-example-container-app.md#application-manifest)|
|[设置隔离模式](service-fabric-get-started-containers.md#configure-isolation-mode)|[容器应用程序清单](service-fabric-manifest-example-container-app.md#application-manifest)|
|[指定特定于 OS 内部版本的容器映像](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[容器应用程序清单](service-fabric-manifest-example-container-app.md#application-manifest)|
|[设置环境变量](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[容器 FrontEndService 服务清单](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)、[容器 BackEndService 服务清单](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[配置终结点](service-fabric-get-started-containers.md#configure-communication)|[容器 FrontEndService 服务清单](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)、[容器 BackEndService 服务清单](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)、[VotingData 服务清单](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|将命令传递到容器|[容器 FrontEndService 服务清单](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[将证书导入到容器中](service-fabric-securing-containers.md)|[容器 FrontEndService 服务清单](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[配置卷驱动程序](service-fabric-containers-volume-logging-drivers.md)|[容器 BackEndService 服务清单](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

