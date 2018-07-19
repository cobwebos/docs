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
ms.openlocfilehash: 629b6fba9ced5fa2ccf22f473fe25c87d1cc4818
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436804"
---
# <a name="azure-service-fabric-security-overview"></a>Azure Service Fabric 安全性概述
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) 是一种分布式系统平台，适用于打包、部署和管理可缩放的可靠微服务。 Service Fabric 解决了开发和管理云应用程序的难题。 开发人员和管理员不需解决复杂的基础结构问题，只需专注于实现苛刻的任务关键型工作负荷，即那些可缩放且可靠的工作负荷。

本文概述 Service Fabric 部署的安全注意事项。

## <a name="secure-your-cluster"></a>保护群集
Azure Service Fabric 协调跨计算机群集的服务。 必须保护群集以防止未经授权的用户连接到群集，特别是群集上正在运行生产工作负荷时。 尽管可能会创建不安全的群集，但是匿名用户可与群集建立连接（当这种群集向公共 Internet 公开管理终结点时）。

对于独立运行的或 Azure 上运行的群集，要考虑的两种方案是节点到节点安全性，以及客户端到节点安全性。 可以使用各种技术来实现这些方案。

### <a name="node-to-node-security"></a>节点到节点安全性
节点到节点安全性适用于群集中 VM 或计算机之间的通信。 使用节点到节点安全性，可确保只有已获授权加入群集的计算机才能参与托管应用程序和群集中的服务。

在 Azure 上运行的群集或在 Windows 上运行的独立群集可以使用[证书安全性](https://msdn.microsoft.com/library/ff649801.aspx)或适用于 Windows Server 计算机的 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

#### <a name="node-to-node-certificate-security"></a>节点到节点的证书安全性

创建群集时，Service Fabric 将使用指定的 X.509 服务器证书。 有关这些证书是什么以及如何获取或创建这些证书的概述，请参阅[使用证书](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)。

证书安全性是在通过 Azure 门户、Azure 资源管理器模板或独立的 JSON 模板创建群集时配置的。 可以指定一个主要证书和一个可选的辅助证书（用于证书滚动更新）。 指定的主证书和辅助证书应不同于为[客户端到节点安全性](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)指定的管理员客户端证书和只读客户端证书。

### <a name="client-to-node-security"></a>客户端到节点安全性
使用客户端标识配置客户端到节点安全性。 若要在客户端与群集之间建立信任关系，必须对群集进行配置，使其知道可以信任哪些客户端标识。

Service Fabric 针对连接到 Service Fabric 群集的客户端支持两种访问控制类型：

-   **管理员**：对管理功能（包括读取/写入功能）拥有完全访问权限。
-   **用户**：只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。

通过使用访问控制，群集管理员可以限制对某些类型的群集操作的访问。 这就使得群集更加安全。

#### <a name="client-to-node-certificate-security"></a>客户端到节点的证书安全性

客户端到节点证书安全性是在通过 Azure 门户、资源管理器模板或独立的 JSON 模板创建群集时配置的。 你需要指定管理客户端证书和/或用户客户端证书。 请确保这些证书应不同于为节点到节点安全性指定的主要证书和辅助证书。

客户端如果使用管理员证书连接到群集，则拥有管理功能的完全访问权限。 客户端如果使用只读的用户客户端证书连接到群集，则只拥有管理功能的只读访问权限。 换言之，这些证书用于基于角色的访问控制 (RBAC)。

若要了解如何在群集中配置证书安全性，请参阅[使用 Azure 资源管理器模板设置群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)。

#### <a name="client-to-node-azure-active-directory-security"></a>Azure Active Directory 客户端到节点安全性

在 Azure 上运行的群集也可以使用 Azure Active Directory (Azure AD) 来保护对管理终结点的访问。 若要了解如何创建必要的 Azure Active Directory 项目，如何在创建群集期间填充这些项目，以及如何连接这些群集，请参阅[使用 Azure 资源管理器模板设置群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)。

通过 Azure AD，组织（称为租户）可管理用户对应用程序的访问。 提供有基于 Web 登录 UI 的应用程序和采用本地客户端体验的应用程序。

Service Fabric 群集提供其管理功能的各种入口点，包括基于 Web 的 Service Fabric Explorer 和 Visual Studio。 因此，需要创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。

对于 Azure 群集，建议针对节点到节点安全性使用 Azure AD 安全性来验证客户端和证书。

对于使用 Windows Server 2012 R2 和 Active Directory 的独立 Windows Server 群集，建议结合使用 Windows 安全性和组托管服务帐户 (gMSA)。 否则，可以结合使用 Windows 安全性和 Windows 帐户。

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>了解 Service Fabric 中的监视和诊断
[监视和诊断](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview)对任何环境中的开发、测试和部署应用程序和服务都至关重要。 监视和诊断有助于确保应用程序和服务在本地开发环境或生产环境中按预期运行，实现监视和诊断时，Service Fabric 解决方案效果最佳。

从安全角度来看，监视和诊断的主要目标是：

-   检测和诊断可能由安全事件引发的硬件和基础结构问题。
-   检测可能为危害指标 (IoC) 的软件和应用问题。
-   了解资源使用情况，防止因疏忽招致的拒绝服务。

监视和诊断的工作流分为三个步骤：

1.  **事件生成**：事件生成同时包括基础结构（群集）级别和应用程序/服务级别的事件（日志、跟踪、自定义事件）。 请参阅[基础结构级别事件](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra)和[应用程序级别事件](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)，了解所含内容及如何添加进一步检测。

2.  **事件聚合**：需要先收集和聚合生成的事件才能显示这些事件。 通常建议使用 [Azure 诊断](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad)（类似于基于代理的日志集合）或 [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow)（进程内日志集合）。

3.  **分析**：需可视化事件并能够以某种格式访问事件，以便进行分析和显示。 有多个平台可用于监视和诊断数据的分析和可视化。 我们建议使用 [Azure Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) 和 [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights)，因为它们能与 Service Fabric 完美集成。

还可使用 [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) 来监视构建 Service Fabric 群集时所在的许多 Azure 资源。

监视器是一个独立的服务，可以监视各个服务的运行状况和负载，并报告运行状况模型层次结构中任何组件的运行状况。 使用监视器有助于防止出现基于单个服务的视图所检测不到的错误。 

监视器也是一个托管代码的好选择，在此无需用户交互即可执行补救措施。 例如，每隔特定时间就清理一次存储中的日志文件。 可以在 [Azure Service Fabric 监视器示例](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/)中找到监视器服务实现示例。

## <a name="secure-communication-by-using-certificates"></a>使用证书来保护通信
证书有助于保护独立 Windows 群集的不同节点之间的通信。 通过使用 X.509 证书，还可以对连接到此群集的客户端进行身份验证。 这可确保只有授权的用户可以访问群集。 建议在创建群集时启用群集上的证书。

X.509 数字证书通常用于验证客户端与服务器。 它们还用于对消息进行加密和数字签名。

下表列出了在设置群集时所需的证书：

|证书信息设置 |说明|
|-------------------------------|-----------|
|ClusterCertificate|    需要使用此证书来保护群集节点之间的通信。 可以使用两个群集证书：一个主要证书，一个用于升级的辅助证书。|
|ServerCertificate| 当客户端尝试连接到此群集时，系统会向客户端提供此证书。 可以使用两个服务器证书：一个主要证书，一个用于升级的辅助证书。|
|ClientCertificateThumbprints|  这是要在经过身份验证的客户端上安装的一组证书。|
|ClientCertificateCommonNames|  这是 CertificateCommonName 的第一个客户端证书的通用名称。 CertificateIssuerThumbprint 是此证书颁发者的指纹。|
|ReverseProxyCertificate|   若要保护[反向代理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)，可以选择指定此证书。|

有关保护证书的详细信息，请参阅[使用 X.509 证书保护 Windows 上的独立群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)。

## <a name="understand-role-based-access-control"></a>了解基于角色的访问控制
可在创建群集时为每个角色提供不同的标识（包括证书），从而指定管理员和用户客户端角色。 若要详细了解默认访问控制设置以及如何更改默认设置，请参阅 [Service Fabric 客户端的基于角色的访问控制](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)。

## <a name="secure-standalone-clusters-by-using-windows-security"></a>使用 Windows 安全性保护独立群集
为了防止有人未经授权访问某个 Service Fabric 群集，必须保护该群集。 当群集运行生产工作负荷时，安全性就尤为重要。 在 ClusterConfig.JSON 文件中使用 Windows 安全性配置节点到节点和客户端到节点的安全性。

如果需要在 gMSA 下运行 Service Fabric，可通过设置 [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) 来配置节点到节点安全性。 若要在节点之间建立信任关系，这些节点必须能够相互识别。

如果希望在 Active Directory 域内使用计算机组，可通过设置 ClusterIdentity 来配置节点到节点安全性。 有关详细信息，请参阅[在 Active Directory 中创建计算机组](https://msdn.microsoft.com/library/aa545347)。

使用 ClientIdentities 配置客户端到节点安全性。 必须对群集进行配置，使其识别可以信任的客户端标识。 可通过两种方式建立信任：

-   指定用户可以连接到的域组。
-   指定用户可以连接到的域节点。

## <a name="configure-application-security-in-service-fabric"></a>在 Service Fabric 中配置应用程序安全性
### <a name="manage-secrets-in-service-fabric-applications"></a>管理 Service Fabric 应用程序中的机密
机密可以是任何敏感信息，例如存储连接字符串、密码或其他不应以明文形式处理的值。

可以使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 来管理密钥和机密。 但是，在应用程序中使用机密与特定的云平台无关。 可将应用程序部署到在任意位置托管的群集。 此流程包括四个主要步骤：

1.  获取数据加密证书。
2.  在群集中安装证书。
3.  在部署应用程序时使用证书加密机密值，并将其注入服务的 Settings.xml 配置文件。
4.  通过使用相同的加密证书进行解密，从 Settings.xml 中读取加密值。

有关详细信息，请参阅[管理 Service Fabric 应用程序中的机密](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="configure-security-policies-for-an-application"></a>配置应用程序的安全策略
Azure Service Fabric 安全性能够保护群集中以不同用户帐户运行的应用程序。 Service Fabric 安全性还有助于在部署时使用用户帐户来保护应用程序所用的资源，例如文件、目录和证书。 这样，即使在共享托管环境中，也可确保运行的应用程序更安全。

配置安全策略的任务包括：

-   配置服务安装程序入口点的策略
-   从安装程序入口点启动 PowerShell 命令
-   对本地调试使用控制台重定向
-   配置服务代码包的策略
-   为 HTTP 和 HTTPS 终结点分配安全访问策略

## <a name="secure-communication-for-services"></a>保护服务的通信
安全是通信最为重视的要素之一。 Reliable Services 应用程序框架提供了一些预先构建的通信堆栈和工具，可用来提高安全性。 有关详细信息，请参阅[保护服务的服务远程通信](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)。

## <a name="next-steps"></a>后续步骤
- 有关群集安全性的概念信息，请参阅[使用 Azure 资源管理器创建 Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)和[使用 Azure 门户创建 Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal)。
- 若要了解有关 Service Fabric 中群集安全性的详细信息，请参阅 [Service Fabric 群集安全方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)。
