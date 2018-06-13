---
title: 了解 Azure Service Fabric 应用程序安全性 | Microsoft Docs
description: 有关如何在 Service Fabric 中安全运行微服务应用程序的概述。 了解如何以不同的安全帐户运行服务和启动脚本、对用户进行身份验证和授权、管理应用程序机密、保护服务通信、使用 API 网关，以及保护应用程序的静态数据。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: fa6d46186ad833b68e60c24f742d210b7845759a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207904"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric 应用程序和服务安全性
微服务体系结构可以带来[诸多好处](service-fabric-overview-microservices.md)。 但是，管理微服务的安全性有一定的难度，比管理传统单体式应用程序的安全性更复杂。 

单体式应用程序通常在网络中的一台或多台服务器上运行，因此更容易识别公开的端口和 API 以及 IP 地址。 往往只需保护一个周边或边界，以及一个数据库。 如果安全漏洞或攻击导致系统泄露，该系统中的所有信息可能都会让攻击者利用。 包含微服务的系统更加复杂。  这些服务是分散式的，分布在多个主机之间，并且可从一台主机迁移到另一台主机。  如果采用适当的安全措施，则可以限制攻击者可以获取的特权，以及在单次攻击中破解一个服务后可以获取的数据量。  通信不是在内部进行，而是通过网络发生的，并且有许多公开的端口，服务之间存在大量的交互。 了解具体的服务交互活动以及交互何时发生，对于应用程序的安全至关重要。

本文不会提供有关微服务安全性的指导，因为网络上有大量此类资源，而是介绍如何在 Service Fabric 中实现安全性的各个层面。

## <a name="authentication-and-authorization"></a>身份验证和授权
通常，有必要将服务公开的资源和 API 限制给特定的受信任用户或客户端使用。 身份验证是可靠认定用户身份的过程。  授权是将 API 或服务提供给某些已经过身份验证的用户，而不提供给其他用户使用的过程。

### <a name="authentication"></a>身份验证
做出 API 级信任决策的第一个步骤就是身份验证。 身份验证是可靠认定用户身份的过程。  在微服务场景中，身份验证通常以集中方式处理。 如果使用 API 网关，可[将身份验证任务卸载](/azure/architecture/patterns/gateway-offloading)到网关。 如果使用此方法，请确保除非部署了额外的安全措施来对消息（不管是否来自网关）进行身份验证，否则不能直接访问单个服务（在不使用 API 网关的情况下）。

如果可以直接访问服务，则可以使用某个身份验证服务（例如 Azure Active Directory，或充当安全令牌服务 (STS) 的专用身份验证微服务）对用户进行身份验证。 信任决策在包含安全令牌或 Cookie 的服务之间共享。 

对于 ASP.NET Core，用于[对用户进行身份验证](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/)的主要机制是 ASP.NET Core 标识成员身份系统。 ASP.NET Core 标识在开发人员配置的数据存储中存储用户信息（包括登录信息、角色和声明）。 ASP.NET Core 标识支持双重身份验证。  也支持外部身份验证提供程序，因此，用户可以使用 Microsoft、Google、Facebook 或 Twitter 等提供程序中的现有身份验证过程登录。 

### <a name="authorization"></a>授权
完成身份验证后，服务需要为用户访问授权，或确定哪些用户可以访问。 此过程可让服务将 API 提供给某些经过身份验证的用户使用，而不是提供给所有用户使用。 授权是正交性的，它独立于身份验证，是认定用户身份的过程。 身份验证可为当前用户创建一个或多个标识。

可以根据用户的角色或者根据自定义策略（可能包括检查声明或其他试探方法）实现 [ASP.NET Core 授权](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications)。

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>使用 API 网关限制和保护访问
云应用程序通常都需要使用前端网关，为用户、设备或其他应用程序提供同一个入口点。 [API 网关](/azure/architecture/microservices/gateway)位于客户端与服务之间，是应用程序提供的所有服务的入口点。 它充当反向代理，将来自客户端的请求路由到服务。 它还可以执行各种横切任务，例如身份验证和授权、SSL 终止与速率限制。 如果未部署网关，则客户端必须直接向前端服务发送请求。

在 Service Fabric 中，网关可以是任意无状态服务（例如 [ASP.NET Core 应用程序](service-fabric-reliable-services-communication-aspnetcore.md)），也可以是其他专为流量入口设计的服务（例如 [Træfik](https://docs.traefik.io/)、[事件中心](https://docs.microsoft.com/azure/event-hubs/)、[IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或 [Azure API 管理](https://docs.microsoft.com/azure/api-management)）。

API 管理直接与 Service Fabric 集成，以便可以使用一组丰富的路由规则向后端 Service Fabric 服务发布 API。  可以使用限制来保护对后端服务的访问、防止 DOS 攻击；还可以验证 API 密钥、JWT 令牌、证书和其他凭据。 有关详细信息，请参阅[有关 Service Fabric 与 Azure API 管理的概述](service-fabric-api-management-overview.md)。

## <a name="manage-application-secrets"></a>管理应用程序机密
机密可以是任何敏感信息，例如存储连接字符串、密码或其他不应以明文形式处理的值。 本文使用 Azure Key Vault 来管理密钥和机密。 但是，在应用程序中*使用*机密的方式不区分云平台，因此可让应用程序部署到托管在任何位置的群集。

建议通过[服务配置包][config-package]来管理服务配置设置。 可以通过包含运行状况验证和自动回滚的托管滚动升级机制来控制配置包版本以及对其进行更新。 这比全局配置更有优势，因为可以减少全局服务中断的可能性。 加密的机密也不例外。 通过 Service Fabric 的内置功能，可以使用证书加密来加密和解密配置包 Settings.xml 文件中的值。

下图演示了 Service Fabric 应用程序中机密管理的基本流程：

![机密管理概述][overview]

此流程包括四个主要步骤：

1. 获取数据加密证书。
2. 在群集中安装证书。
3. 在部署应用程序时使用证书加密机密值，并将其注入服务的 Settings.xml 配置文件。
4. 通过使用相同的加密证书进行解密，从 Settings.xml 中读取加密值。 

[Azure 密钥保管库][key-vault-get-started]在此处用作证书的安全存储位置，也可用于将证书安装在 Azure 中的 Service Fabric 群集上。 如果不部署到 Azure，则不需要使用密钥保管库来管理 Service Fabric 应用程序中的机密。

有关示例，请参阅[管理应用程序机密](service-fabric-application-secret-management.md)。

## <a name="secure-the-hosting-environment"></a>保护宿主环境
使用 Azure Service Fabric，可以保护群集中以不同用户帐户运行的应用程序。 Service Fabric 还有助于在部署时使用用户帐户来保护应用程序所用的资源，例如文件、目录和证书。 这样，即使在共享托管环境中，也可确保运行的应用程序彼此更安全。

应用程序清单声明运行服务和保护资源时所需的安全主体（用户和组）。  这些安全主体在运行方式帐户、终结点绑定、包共享或安全访问策略等策略中引用。  然后，将策略应用到应用程序清单的 **ServiceManifestImport** 节中的服务资源。

声明主体时，还可以定义和创建用户组，以便将一个或多个要统一管理的用户添加到每个组。 如果不同的服务入口点有多个用户，而且这些用户需要拥有可在组级别使用的某些常用权限，则这种做法特别有用。

默认情况下，Service Fabric 应用程序在运行 Fabric.exe 程序的帐户之下运行。 借助 Service Fabric，还可以使用应用程序清单中指定的本地用户帐户或本地系统帐户运行应用程序。 有关详细信息，请参阅[以本地用户帐户或本地系统帐户运行服务](service-fabric-application-runas-security.md)。  还可[以本地用户或系统帐户身份运行服务启动脚本](service-fabric-run-script-at-service-startup.md)。

在 Windows 独立群集上运行 Service Fabric 时，可以使用 [Active Directory 域帐户](service-fabric-run-service-as-ad-user-or-group.md)或[组托管服务帐户](service-fabric-run-service-as-gmsa.md)运行服务。

## <a name="secure-containers"></a>保护容器
Service Fabric 提供一种机制，供容器内服务访问在 Windows 或 Linux 群集（5.7 或更高版本）的节点中安装的证书。 此 PFX 证书可以用于对应用程序或服务或与其他服务的安全通信进行身份验证。 有关详细信息，请参阅[将证书导入容器](service-fabric-securing-containers.md)。

此外，Service Fabric 还支持 Windows 容器的 gMSA（组托管服务帐户）。 有关详细信息，请参阅[设置 Windows 容器的 gMSA](service-fabric-setup-gmsa-for-windows-containers.md)。

## <a name="secure-service-communication"></a>保护服务通信
在 Service Fabric 中，服务在 Service Fabric 群集（通常分布在多个 VM 间）中的某个位置运行。 Service Fabric 提供多个选项用于保护服务通信。

可以在 [ASP.NET Core 或 Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) Web 服务中启用 HTTPS 终结点。

可以在反向代理与服务之间建立安全连接，从而启用端到端安全通道。 只有将反向代理配置为侦听 HTTPS 时，才支持连接到安全服务。 有关配置反向代理的信息，请参阅 [Azure Service Fabric 中的反向代理](service-fabric-reverseproxy.md)。  [连接到安全服务](service-fabric-reverseproxy-configure-secure-communication.md)介绍了如何在反向代理与服务之间建立安全连接。

Reliable Services 应用程序框架提供了一些预先构建的通信堆栈和工具，可用来提高安全性。 了解如何在使用服务远程处理（在 [C#](service-fabric-reliable-services-secure-communication.md) 或 [Java](service-fabric-reliable-services-secure-communication-java.md) 中）或 [WCF](service-fabric-reliable-services-secure-communication-wcf.md) 时提高安全性。

## <a name="encrypt-application-data-at-rest"></a>加密应用程序的静态数据
在 Azure 中运行的 Service Fabric 群集中的每个[节点类型](service-fabric-cluster-nodetypes.md)都受[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)的支持。 可以使用 Azure 资源管理器模板将数据磁盘附加到组成 Service Fabric 群集的规模集。  如果服务将数据保存到附加的数据磁盘，则你可以[加密这些数据磁盘](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md)，以保护应用程序数据。

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
* [在服务启动时运行安装脚本](service-fabric-run-script-at-service-startup.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)
* [了解群集安全性](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png