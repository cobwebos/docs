---
title: "Azure Service Fabric 安全性最佳做法 | Microsoft Docs"
description: "本文提供有关 Azure Service Fabric 安全性的一套最佳做法。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric 安全性最佳做法
在 Azure 上部署应用程序的过程快速、轻松且经济高效。 在生产环境中部署云应用程序之前，准备好一个最佳做法会很有用，这样可以根据一份必要和建议的最佳做法列表来评估应用程序。

Azure Service Fabric 是一种分布式系统平台，适用于打包、部署和管理可缩放的可靠微服务。 Service Fabric 还解决了开发和管理云应用程序中的重大难题。 开发人员和管理员不需解决复杂的基础结构问题，只需专注于实现苛刻的任务关键型工作负荷，即那些可缩放、可靠且易于管理的工作负荷。 

对于每项最佳做法，本文将说明：

-   最佳实践是什么
-   为何要启用该最佳实践
-   如果无法启用该最佳实践，可能的结果是什么
-   如何学习启用最佳实践

当前提供了以下 Azure Service Fabric 安全性最佳做法：

-   使用 Azure 资源管理器 (ARM) 模板和 Service Fabric Azure PowerShell 模块创建安全群集
-   使用 X.509 证书
-   配置安全性策略
-   Reliable Actors 安全性配置
-   配置适用于 Azure Service Fabric 的 SSL
-   Azure Service Fabric 的网络隔离/网络安全
-   设置 Key Vault 增强安全性
-   将用户分配到角色


## <a name="best-practices-for-securing-your-cluster"></a>保护群集的最佳方案

**大图**

始终使用安全群集
-   群集安全性 – 使用证书
-   客户端访问权限（管理员和只读）– 使用 AAD

使用自动部署
-   使用脚本生成、部署和滚动更新机密
-   将机密保存在 KV 中，使用 AD 实现所有其他客户端访问
-   未经身份验证，任何人都无法访问。

此外，请注意以下几点：
-   使用网络完全组 (NSG) 创建 DMZ
-   使用跳转服务器为群集 VM 提供 RDP 或管理群集

必须保护群集以防止未经授权的用户连接到群集，特别是群集上正在运行生产工作负荷时。 尽管可以创建不安全的群集，但是，当这种群集向公共 Internet 公开管理终结点时，匿名用户可与它建立连接。

用于实现这些方案的技术。 [群集安全性方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)包括：

-   节点到节点安全性 - 保护群集中的 VM 和计算机之间的通信。 这可确保只有已获授权加入群集的计算机可以参与托管应用程序和群集中的服务。
在 Azure 上运行的群集或在 Windows 上运行的独立群集可以使用[证书安全性](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)或适用于 Windows Server 计算机的 [Windows 安全性](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security)。
-   客户端到节点安全性 - 保护 Service Fabric 客户端与群集中单个节点之间的通信。
-   基于角色的访问控制 (RBAC) - 在创建群集时分别为管理员和用户客户端角色提供单独的标识（证书、AAD 等），指定这两个角色。
-   安全性建议 - 对于 Azure 群集，建议针对节点到节点安全性使用 AAD 安全性来验证客户端和证书。

若要配置 Windows 独立群集，请参阅 [Windows 独立群集的配置设置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)。

使用 Azure 资源管理器模板和 Service Fabric Azure PowerShell 模块创建安全性群集。
[此处](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)逐步介绍如何使用 Azure 资源管理器在 Azure 中设置 Azure Service Fabric 安全性群集。

使用 Azure 资源管理器模板自定义群集
-   VM VHD 的安装管理存储

使用 Azure 资源管理器模板对资源组作出更改
-   轻松配置管理
-   审核

将群集配置视为 Code
-   彻底检查选择进行部署的配置
-   避免使用隐式命令直接调整资源

可以对 [Service Fabric 应用程序生命周期](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle)的许多层面进行自动化。 可以使用 [Service Fabric Azure PowerShell 模块](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package)自动完成部署、升级、删除和测试 Azure Service Fabric 应用程序的常见任务。 还提供了用于应用管理的托管和 HTTP API。

## <a name="use-x509-certificates"></a>使用 X.509 证书
始终应使用 X.509 证书或 Windows 安全性保护群集。 安全性只能在群集创建时配置，不能在创建群集以后启用安全性。

如果要指定[群集证书](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)，请将 ClusterCredentialType 的值设置为 X509。 如果要为外部连接指定服务器证书，请将 ServerCredentialType 设置为 X509。

-   运行生产工作负荷的群集中使用的证书应使用正确配置的 Windows Server 证书服务进行创建，或者从已批准的证书颁发机构 (CA) 获取。
-   切勿在生产环境中使用通过 MakeCert.exe 等工具创建的临时或测试证书。
-   可以使用自签名证书，但只应使用它来测试群集，而不应在生产环境中使用。

如果此群集不安全。 任何人都可以进行匿名连接并执行管理操作，因此生产型群集应始终使用 X.509 证书或 Windows 安全措施来确保安全性。

若要详细了解如何在 Service Fabric 群集中启用证书，请参阅[添加或删除 Service Fabric 群集的证书](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)。

## <a name="configure-security-policies"></a>配置安全性策略
Service Fabric 还有助于在部署时使用用户帐户来保护应用程序所用的资源，例如文件、目录和证书。 这样，即使在共享托管环境中，也可确保运行的应用程序彼此更安全。

-   使用 Active Directory 域组或用户：可以运行 Active Directory 用户或组帐户的凭据下的服务。 这是域中的本地 Active Directory，不是 Azure Active Directory (Azure AD)。 通过使用域用户或组，可以访问域中已被授予权限的其他资源（例如文件共享）。

-   为 HTTP 和 HTTPS 终结点分配安全访问策略：如果向服务应用 RunAs 策略，而服务清单声明具有 HTTP 协议的终结点资源，则必须指定 SecurityAccessPolicy，以确保分配给这些终结点的端口都已针对用来运行服务的 RunAs 用户帐户正确列入访问控制列表中。 否则，http.sys 将无权访问服务，并且将无法从客户端调用。
若要深入了解如何在 Service Fabric 中启用安全性策略，请参阅[配置应用程序的安全性策略](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security)。

## <a name="reliable-actors-security-configuration"></a>Reliable Actors 安全性配置
Service Fabric Reliable Actors 是执行组件设计模式的实现。 与任何软件设计模式一样，决定是否使用特定的模式取决于遇到的软件设计问题是否适合用该模式解决。

通常对于以下情况，请考虑使用执行组件模式来对问题或场景进行建模：
-   问题空间包含大量（几千或更多）小型、独立的状态和逻辑单元。
-   想要使用单线程对象，这些对象无需外部组件的明显交互，包括查询一组执行组件的状态。
-   执行组件实例不会通过发出 I/O 操作来使用不可预测的延迟阻止调用方。

在 Service Fabric 中，执行组件在 Reliable Actors 框架（在 [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) 的基础上构建而成的基于执行组件模式的应用程序框架）中实现。 编写的每个 Reliable Actor 服务实际上都是一个已分区、有状态的 Reliable Service。
每个执行组件都定义为执行组件类型的一个实例，类似于 .NET 对象是 .NET 类型的一个实例。 例如，可能有用于实现计算器功能的执行组件类型，并可能有很多在群集中各个节点分布的该类型的执行组件。 每个此类执行组件都通过执行组件 ID 进行唯一标识。

[复制器安全配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration)用于保护在复制过程中使用的信道的安全。 这意味着服务将无法看到对方的复制流量，从而确保高度可用的数据也处于安全状态。 默认情况下，空的安全配置节会影响复制安全。
复制器配置用于配置负责使执行组件状态提供程序状态高度可靠的复制器。

## <a name="configure-ssl-for-azure-service-fabric"></a>配置适用于 Azure Service Fabric 的 SSL

服务器身份验证：在管理客户端上对群集管理终结点进行[身份验证](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)，使管理客户端知道它正在与真正的群集通信。 此证书还通过 HTTPS 为 HTTPS 管理 API 和 Service Fabric Explorer 提供 [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm)。
必须获取群集的自定义域名。 从 CA 请求证书时，该证书的使用者名称必须与用于群集的自定义域名匹配。

要为应用程序配置 SSL，首先需要获取已由证书颁发机构 (CA)（出于此目的颁发证书的受信任的第三方）签署的 SSL 证书。 如果尚未获取 SSL 证书，将需要从销售 SSL 证书的公司购买一个 SSL 证书。

该证书必须满足 Azure 中的以下 SSL 证书要求：
-   证书必须包含私钥。
-   必须为密钥交换创建证书，并且该证书可导出到个人信息交换 (.pfx) 文件。
-   证书的使用者名称必须与用于访问云服务的域匹配。 无法从证书颁发机构 (CA) 处获取针对 cloudapp.net 域的 SSL 证书。 必须获取在访问服务时要使用的自定义域名。 在从 CA 请求证书时，该证书的使用者名称必须与用于访问应用程序的自定义域名匹配。 例如，如果自定义域名为 contoso.com，则将要从 CA 请求用于 contoso.com 或 www.contoso.com 的证书。
-   该证书必须使用至少 2048 位加密。

HTTP 不安全且易受窃听攻击，因为 Web 浏览器与 Web 服务器之间或其他终结点之间传输的数据是纯文本形式。 这意味着攻击者可以截获和查看敏感数据，例如信用卡详细信息和帐户登录名。 使用 HTTPS 通过浏览器发送或发布数据时，SSL 可确保此类信息经过加密且不被截取。

有关详细信息，请参阅[为 Azure 应用程序配置 SSL](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate)。

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Azure Service Fabric 的网络隔离/网络安全
以 [Azure 资源管理器 (ARM) 模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)为例，设置三个 NodeType 安全群集，使用网络安全组控制入站和出站网络流量。

该模板为每个虚拟机规模集 (VMSS) 都提供了一个网络安全组，以控制 VMSS 的入站和出站流量。 默认情况下，规则设置为允许系统服务所需的所有流量以及在模板中指定的应用程序端口。 查看这些规则并进行更改以适应需要，包括向应用程序添加任何新规则。

有关详细信息，请参阅 [Azure Service Fabric – 常见网络方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)。

## <a name="set-up-a-key-vault-for-security"></a>设置 Key Vault 增强安全性
证书在 Service Fabric 中用于提供身份验证和加密，为群集及其应用程序提供全方位的保护。

Service Fabric 使用 X.509 证书保护群集，提供应用程序安全功能。 Key Vault 用于管理 Azure 中 Service Fabric 群集的 [证书](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)。 在 Azure 中部署群集时，负责创建 Service Fabric 群集的 Azure 资源提供程序将从密钥保管库提取证书，并将其安装在群集 VM 上。

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)、Service Fabric 群集与 Azure 资源提供程序（在创建群集时使用 Key Vault 中存储的证书）之间的关系。

**创建资源组**第一个步骤是专门为 Key Vault 创建资源组。 建议将密钥保管库置于其资源组中。 这样可在不丢失密钥和机密的情况下删除计算和存储资源组，包括具有 Service Fabric 群集的资源组。 包含 Key Vault 的资源组必须与正在使用它的群集位于同一区域。

**在新资源组中创建 Key Vault** 必须针对部署启用 Key Vault，使计算资源提供程序能够从中获取证书并将其安装在虚拟机实例上。
若要深入了解如何设置 Azure Key Vault，请参阅 [Azure Key Vault 快速入门](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)。

## <a name="assign-users-roles"></a>为用户分配角色
创建用于表示群集的应用程序后，请将用户分配到 Service Fabric 支持的角色：只读和管理员。 可通过使用 Azure 经典门户来分配角色。

>[!Note]
> 有关 Service Fabric 中角色的详细信息，请参阅[适用于 Service Fabric 客户端的基于角色的访问控制](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)。

Azure Service Fabric 针对连接到 [Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)的客户端支持两种不同的访问控制类型：管理员和用户。 访问控制可让群集管理员针对不同的用户组限制特定群集操作的访问权限，使群集更加安全。

## <a name="next-steps"></a>后续步骤
- 设置 Service Fabric [开发环境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)。
- 了解 [Service Fabric 支持选项](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)。


