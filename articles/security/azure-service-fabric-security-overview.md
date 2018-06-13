---
title: Azure Service Fabric 安全性概述 | Microsoft Docs
description: 本文简要介绍 Azure Service Fabric 安全性。
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 19e4e55c4bd63e5d7a9ef6ea3b0bf6ae63f929d5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895657"
---
# <a name="azure-service-fabric-security-overview"></a>Azure Service Fabric 安全性概述
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) 是一种分布式系统平台，适用于打包、部署和管理可缩放的可靠微服务。 Service Fabric 解决了开发和管理云应用程序的重大难题。 开发人员和管理员不需解决复杂的基础结构问题，只需专注于实现苛刻的任务关键型工作负荷，即那些可缩放、可靠且易于管理的工作负荷。

这篇“Azure Service Fabric 安全性概述”重点介绍以下几个方面：

-   保护群集
-   了解监视和诊断
-   通过使用证书营造更安全的环境
-   使用基于角色的访问控制 (RBAC)
-   通过使用 Windows 安全性来保护群集
-   在 Service Fabric 中配置应用程序安全性
-   在 Azure Service Fabric 中保护服务通信 

## <a name="secure-your-cluster"></a>保护群集
Azure Service Fabric 协调跨计算机群集的服务。 必须保护群集以防止未经授权的用户连接到群集，特别是群集上正在运行生产工作负荷时。 尽管可能会创建不安全的群集，但是匿名用户可与它建立连接（当这种群集向公共 Internet 公开管理终结点时）。

本部分将简要介绍有关独立运行或在 Azure 上运行的群集的安全方案。 此外，还介绍了用于实现这些方案的各种方法。 群集安全方案包括：

-   节点到节点安全性
-   客户端到节点安全性

### <a name="node-to-node-security"></a>节点到节点安全性
节点到节点安全性可保护群集中 VM 或计算机之间的通信。 使用节点到节点安全性，可确保只有已获授权加入群集的计算机才能参与托管应用程序和群集中的服务。

在 Azure 上运行的群集或在 Windows 上运行的独立群集可以使用[证书安全性](https://msdn.microsoft.com/library/ff649801.aspx)或适用于 Windows Server 计算机的 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

**了解节点到节点的证书安全性**

创建群集时，Service Fabric 将使用指定的 X.509 服务器证书。 有关这些证书是什么以及如何获取或创建这些证书的概述，请参阅[使用证书](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)。

证书安全性是在通过 Azure 门户、Azure 资源管理器模板或独立的 JSON 模板创建群集时配置的。 可以指定一个主要证书和一个可选的辅助证书（用于证书滚动更新）。 指定的主证书和辅助证书应不同于为[客户端到节点安全性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)指定的管理员客户端证书和只读客户端证书。

### <a name="client-to-node-security"></a>客户端到节点安全性
使用客户端标识配置客户端到节点安全性。 若要在客户端与群集之间建立信任关系，必须对群集进行配置，使其知道可以信任哪些客户端标识。 可以通过两种不同方法实现此目的：

-   指定用户可以连接到的域组。 
-   指定用户可以连接到的域节点。

Service Fabric 针对连接到 Service Fabric 群集的客户端支持两种不同的访问控制类型：

-   管理员
-   用户

通过使用访问控制，群集管理员可以限制对某些类型的群集操作的访问。 这就使得群集更加安全。

 管理员对管理功能（包括读取/写入功能）拥有完全访问权限。 默认情况下，用户只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。

**了解客户端到节点的证书安全性**

客户端到节点证书安全性是在通过 Azure 门户、资源管理器模板或独立的 JSON 模板创建群集时配置的。 你需要指定管理客户端证书和/或用户客户端证书。 

指定的管理员客户端证书和用户客户端证书应不同于为节点到节点安全性指定的主证书和辅助证书。

客户端如果使用管理员证书连接到群集，则拥有管理功能的完全访问权限。 客户端如果使用只读的用户客户端证书连接到群集，则只拥有管理功能的只读访问权限。 换言之，这些证书将用于 RBAC。

若要了解如何在群集中配置证书安全性，请参阅[使用 Azure 资源管理器模板设置群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)。

**了解 Azure 上客户端到节点的 Azure Active Directory 安全性**

在 Azure 上运行的群集也可以使用 Azure Active Directory (Azure AD) 来保护对管理终结点的访问。 若要了解如何创建必要的 Azure Active Directory 项目，如何在创建群集期间填充这些项目，以及如何连接这些群集，请参阅[使用 Azure 资源管理器模板设置群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)。

通过 Azure AD，组织（称为租户）可管理用户对应用程序的访问。 提供有基于 Web 登录 UI 的应用程序和采用本地客户端体验的应用程序。

Service Fabric 群集提供其管理功能的各种入口点，包括基于 Web 的 Service Fabric Explorer 和 Visual Studio。 因此，需要创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。

对于 Azure 群集，建议针对节点到节点安全性使用 Azure AD 安全性来验证客户端和证书。

对于使用 Windows Server 2012 R2 和 Active Directory 的独立 Windows Server 群集，建议结合使用 Windows 安全性和组管理帐户。  否则，可以结合使用 Windows 安全性和 Windows 帐户。

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>了解在 Azure Service Fabric 中进行监视和诊断
[监视和诊断](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview)对任何环境中的开发、测试和部署应用程序和服务都至关重要。 监视和诊断有助于确保应用程序和服务在本地开发环境或生产环境中按预期运行，实现监视和诊断时，Service Fabric 解决方案效果最佳。

从安全角度来看，监视和诊断的主要目标是：

-   检测和诊断可能由安全事件引发的硬件和基础结构问题。
-   检测可能为危害指标 (IoC) 的软件和应用问题。
-   了解资源使用情况，防止因疏忽招致的拒绝服务。

监视和诊断的整体工作流程分为三个步骤：

-   事件生成：事件生成同时包括基础结构（群集）级别和应用程序/服务级别的事件（日志、跟踪、自定义事件）。 请参阅[基础结构级别事件](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra)和[应用程序级别事件](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)，了解所含内容及如何添加进一步检测。

-   事件聚合：需要先收集和聚合生成的事件才能显示这些事件。 通常建议使用 [Azure 诊断](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad)（类似于基于代理的日志集合）或 [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow)（进程内日志集合）。

-   分析：需可视化事件并能够以某种格式访问事件，以便进行分析和显示。 有多个平台可用于监视和诊断数据的分析和可视化。 建议使用 [Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) 和 [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) 这两个平台，因为它们能与 Service Fabric 完美集成。

还可使用 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) 来监视构建 Service Fabric 群集时所在的许多 Azure 资源。

监视器是一个独立的服务，可以监视各个服务的运行状况和负载，并报告运行状况模型层次结构中任何组件的运行状况。 使用监视器有助于防止出现基于单个服务的视图所检测不到的错误。 

监视器也是一个托管代码的好选择，在此无需用户交互即可执行补救措施（例如每隔特定时间就清理一次存储中的日志文件）。 可以在 [Azure Service Fabric 监视器示例](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/)中找到监视器服务实现示例。

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>了解如何使用证书来确保安全通信
证书有助于保护独立 Windows 群集的不同节点之间的通信。 通过使用 X.509 证书，还可以对连接到此群集的客户端进行身份验证。 这可确保只有授权的用户可以访问群集。 建议在创建群集时启用群集上的证书。

### <a name="x509-certificates-and-service-fabric"></a>X.509 证书和 Service Fabric
X.509 数字证书通常用于验证客户端与服务器。 它们还用于对消息进行加密和数字签名。

下表列出了在设置群集时所需的证书：

|证书信息设置 |说明|
|-------------------------------|-----------|
|ClusterCertificate|    需要使用此证书来保护群集节点之间的通信。 可以使用两个不同的证书（一个主证书和一个辅助证书）进行升级。|
|ServerCertificate| 当客户端尝试连接到此群集时，系统会向客户端提供此证书。 可以使用两个不同的服务器证书（一个主证书和一个辅助证书）进行升级。|
|ClientCertificateThumbprints|  这是要在经过身份验证的客户端上安装的一组证书。|
|ClientCertificateCommonNames|  这是 CertificateCommonName 的第一个客户端证书的通用名称。 CertificateIssuerThumbprint 是此证书颁发者的指纹。|
|ReverseProxyCertificate|   如果要保护[反向代理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)，可以选择指定此证书。|

有关保护证书的详细信息，请参阅[使用 X.509 证书保护 Windows 上的独立群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)。

## <a name="understand-role-based-access-control"></a>了解基于角色的访问控制
访问控制可让群集管理员针对不同的用户组限制对特定群集操作的访问权限，从而使群集更加安全。 连接到群集的客户端支持两种不同的访问控制类型： 

- 管理员角色
- 用户角色

管理员对管理功能（包括读取/写入功能）拥有完全访问权限。 默认情况下，用户只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。

可在创建群集时为每个角色提供不同的标识（包括证书），从而指定管理员和用户客户端角色。 若要详细了解默认访问控制设置以及如何更改默认设置，请参阅 [Service Fabric 客户端的基于角色的访问控制](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)。

## <a name="secure-standalone-cluster-by-using-windows-security"></a>使用 Windows 安全性保护独立群集
为了防止有人未经授权访问某个 Service Fabric 群集，必须保护该群集。 当群集运行生产工作负荷时，安全性就尤为重要。 本文介绍了如何在 ClusterConfig.JSON 文件中使用 Windows 安全性配置节点到节点和客户端到节点的安全性。

**使用 gMSA 配置 Windows 安全性**

如果需要在 gMSA 下运行 Service Fabric，可通过设置 [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) 来配置节点到节点安全性。 若要在节点之间建立信任关系，这些节点必须能够相互识别。

使用 ClientIdentities 配置客户端到节点安全性。 若要在客户端与群集之间建立信任关系，必须对群集进行配置，使其识别可以信任的客户端标识。

**使用计算机组配置 Windows 安全性**

如果希望在 Active Directory 域内使用计算机组，可通过设置 ClusterIdentity 来配置节点到节点安全性。 有关详细信息，请参阅[在 Active Directory 中创建计算机组](https://msdn.microsoft.com/library/aa545347)。

使用 ClientIdentities 配置客户端到节点安全性。 若要在客户端与群集之间建立信任关系，必须对群集进行配置，使群集识别可以信任的客户端标识。 可通过两种不同的方式建立信任：

-   指定用户可以连接到的域组。
-   指定用户可以连接到的域节点。

## <a name="configure-application-security-in-service-fabric"></a>在 Service Fabric 中配置应用程序安全性
### <a name="manage-secrets-in-service-fabric-applications"></a>管理 Service Fabric 应用程序中的机密
此方法可帮助管理 Service Fabric 应用程序中的机密。 机密可以是任何敏感信息，例如存储连接字符串、密码或其他不应以明文形式处理的值。

本指南使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 来管理密钥和机密。 但是，在应用程序中使用机密与云平台无关。 这意味着应用程序可以部署到在任意位置托管的群集。 此流程包括四个主要步骤：

-   获取数据加密证书。
-   在群集中安装证书。
-   在部署应用程序时使用证书加密机密值，并将其注入服务的 Settings.xml 配置文件。
-   通过使用相同的加密证书进行解密，从 Settings.xml 中读取加密值。

>[!NOTE]
>详细了解[管理 Service Fabric 应用程序中的机密](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="configure-security-policies-for-your-application"></a>配置应用程序的安全策略
Azure Service Fabric 安全性能够保护群集中以不同用户帐户运行的应用程序。 Service Fabric 安全性还有助于在部署时使用用户帐户来保护应用程序所用的资源，例如文件、目录和证书。 这样，即使在共享托管环境中，也可确保运行的应用程序更安全。

步骤包括：

-   配置服务安装程序入口点的策略。
-   从安装程序入口点启动 PowerShell 命令。
-   对本地调试使用控制台重定向。
-   配置服务代码包的策略。
-   为 HTTP 和 HTTPS 终结点分配安全访问策略。

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>在 Azure Service Fabric 安全性中保护服务的通信
安全是通信最为重视的要素之一。 Reliable Services 应用程序框架提供了一些预先构建的通信堆栈和工具，可用来提高安全性。

-   [使用服务远程处理时帮助保护服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [使用基于 WCF 的通信堆栈时帮助保护服务](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>后续步骤
- 有关群集安全性的概念信息，请参阅[使用 Azure 资源管理器创建 Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)和 [Azure 门户](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal)。
- 若要了解有关 Service Fabric 中群集安全性的详细信息，请参阅 [Service Fabric 群集安全性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)。
