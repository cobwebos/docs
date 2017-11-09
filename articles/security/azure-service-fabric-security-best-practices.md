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
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: 682ad79cc5fe4f08051477b7b90ae80981e5d595
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric 安全性最佳做法
在 Azure 上部署应用程序的过程快速、轻松且经济高效。 将云应用程序部署到生产环境前，请先查看有必要遵照和建议的最佳做法列表，了解最好应如何在应用程序中实现群集安全性。

Azure Service Fabric 是一种分布式系统平台，适用于打包、部署和管理可缩放的可靠微服务。 Service Fabric 还解决了开发和管理云应用程序中的重大难题。 开发人员和管理员不需解决复杂的基础结构问题，只需专注于实现苛刻的任务关键型工作负荷，即那些可缩放、可靠且易于管理的工作负荷。 

对于每项最佳做法，本文将说明：

-   最佳做法是什么。
-   应遵照最佳做法的具体原因。
-   如果未遵照最佳做法，会有什么样的后果。
-   如何学会遵照最佳做法。

建议遵照以下 Azure Service Fabric 安全性最佳做法：

-   使用 Azure 资源管理器模板和 Service Fabric PowerShell 模块创建安全群集。
-   使用 X.509 证书。
-   配置安全策略。
-   实现 Reliable Actors 安全配置。
-   为 Azure Service Fabric 配置 SSL。
-   将 Azure Service Fabric 与网络隔离和安全功能结合使用。
-   出于安全考虑，配置 Azure Key Vault。
-   将用户分配到角色。


## <a name="best-practices-for-securing-your-clusters"></a>保护群集的最佳做法

始终使用安全群集：
-   使用证书实现群集安全性。
-   使用 Azure Active Directory (Azure AD) 提供客户端访问权限（管理员访问权限和只读访问权限）。

使用自动部署：
-   使用脚本生成、部署和滚动更新机密。
-   将机密存储在 Azure Key Vault 中，并使用 Azure AD 提供其他所有客户端访问权限。
-   要求必须经过身份验证，才能读取机密。

此外，还请考虑使用以下配置选项：
-   使用 Azure 网络安全组 (NSG) 创建外围网络（亦称为“隔离区 (DMZ)”和“外围子网”）。
-   结合使用跳板机和远程桌面连接，访问群集虚拟机 (VM) 或管理群集。

必须保护群集，以防未经授权的用户连接到群集，特别是当群集在生产环境中运行时。 尽管可以创建不安全群集，但当群集向公共 Internet 公开管理终结点时，匿名用户就可以与它建立连接。

使用各种技术实现群集安全性的[方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)有三种：

-   节点到节点安全性：此方案可保护群集中 VM 与计算机的通信。 这种安全性可确保只有已获授权加入群集的计算机，才能在群集中托管应用程序和服务。
在此方案中，Azure 上运行的群集或 Windows 上运行的独立群集可以使用[证书安全性](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)或 [Windows 安全性](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security)（适用于 Windows Server 计算机）。
-   客户端到节点安全性：此方案可保护 Service Fabric 客户端与群集中各个节点的通信。
-   基于角色的访问控制 (RBAC)：此方案对每个访问群集的管理员和用户客户端角色使用独立标识（证书、Azure AD 等）。 这些角色标识是在创建群集时指定。

>[!NOTE]
>**适用于 Azure 群集的安全性建议**：若要实现节点到节点安全性，请使用 Azure AD 安全性来验证客户端和证书。

若要配置 Windows 独立群集，请参阅 [Windows 独立群集的配置设置](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)。

使用 Azure 资源管理器模板和 Service Fabric PowerShell 模块创建安全群集。
有关如何使用 Azure 资源管理器模板创建安全 Service Fabric 群集的分步说明，请参阅[创建 Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)。

使用 Azure 资源管理器模板：
-   使用此模板为 VM 虚拟硬盘 (VHD) 配置托管存储，从而自定义群集。
-   使用此模板简化配置管理和审核，推动对资源组的更改。

将群集配置视为代码：
-   仔细检查部署配置。
-   避免使用隐式命令直接修改资源。

可以对 [Service Fabric 应用程序生命周期](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle)的许多层面进行自动化。 [Service Fabric PowerShell 模块](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package)可自动执行常见任务，包括部署、升级、删除和测试 Azure Service Fabric 应用程序。 此外，还提供了托管 API 和 HTTP API，可用于管理应用程序。

## <a name="use-x509-certificates"></a>使用 X.509 证书
始终使用 X.509 证书或 Windows 安全性保护群集。 安全性仅在群集创建时进行配置。 无法在群集创建后启用安全性。

若要指定[群集证书](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)，请将 **ClusterCredentialType** 属性的值设置为 X509。 若要为外部连接指定服务器证书，请将 **ServerCredentialType** 属性的值设置为 X509。

此外，还请遵照以下做法：
-   使用正确配置的 Windows Server 证书服务为生产群集创建证书。 也可以从核准证书颁发机构 (CA) 获取证书。
-   切勿对生产群集使用通过 MakeCert.exe 或类似工具创建的临时或测试证书。
-   对测试群集使用自签名证书，但不要对生产群集使用此类证书。

如果是不安全群集，任何人都可以匿名连接到此群集，并执行管理操作。 鉴于此，请务必使用 X.509 证书或 Windows 安全性保护生产群集。

若要详细了解如何使用 X.509 证书，请参阅[添加或删除 Service Fabric 群集的证书](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)。

## <a name="configure-security-policies"></a>配置安全性策略
Service Fabric 还可保护应用程序使用的资源。 在应用程序部署后，文件、目录和证书等资源都会存储在用户帐户下。 借助此功能，即使在共享的托管环境中，也可加强对运行中应用程序的保护，防止其相互影响。

-   使用 Active Directory 域组或用户：使用 Active Directory 用户或组帐户的凭据运行服务。 请务必在域中使用本地 Active Directory，而不是 Azure Active Directory。 使用域用户或组，访问域中已被授予权限的其他资源。 例如，文件共享等资源。

-   为 HTTP 和 HTTPS 终结点分配安全访问策略：指定 SecurityAccessPolicy 属性，在服务清单使用 HTTP 协议声明终结点资源时，向服务应用 RunAs 策略。 分配给 HTTP 终结点的端口是，运行服务所用的 RunAs 用户帐户的正确访问控制列表。 如果未设置此策略，http.sys 将无权访问服务，并且用户也无法从客户端进行调用。

若要了解如何在 Service Fabric 群集中使用安全策略，请参阅[配置应用程序的安全策略](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security)。

## <a name="implement-the-reliable-actors-security-configuration"></a>实现 Reliable Actors 安全配置
Service Fabric Reliable Actors 是执行组件设计模式的实现。 与所有软件设计模式一样，决定是否使用特定模式时需要考虑，此模式是否适合解决软件问题。

一般来说，使用执行组件设计模式有助于构建解决方案，从而处理以下软件问题或安全方案：
-   问题空间包含大量（几千或更多）小型、独立的状态和逻辑单元。
-   使用的是单线程对象，无需与外部组件进行大量交互，包括跨一组执行组件查询状态。
-   执行组件实例不会发出 I/O 操作指令阻止遇到不可预测延迟的调用方。

在 Service Fabric 中，执行组件是在 Reliable Actors 应用程序框架中实现。 此框架以执行组件模式为依据，在 [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) 的基础之上构建而成。 编写的每个可靠执行组件服务都是一个已分区的有状态可靠服务。

每个执行组件都定义为执行组件类型的一个实例，类似于 .NET 对象是 .NET 类型的一个实例。 例如，用于实现计算器功能的执行组件类型可能包含此类型的多个执行组件，这些执行组件跨群集中的各个节点进行分布。 分布的每个执行组件都通过执行组件标识符进行唯一标识。

[复制器安全配置](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration)用于保护在复制过程中使用的信道的安全。 此配置可阻止服务相互窥探复制流量，并确保可用性很高的数据安全。 默认情况下，空的安全配置节会影响复制安全。
复制器配置用于配置负责使执行组件状态提供程序状态高度可靠的复制器。

## <a name="configure-ssl-for-azure-service-fabric"></a>配置适用于 Azure Service Fabric 的 SSL
服务器身份验证流程向管理客户端[验证](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)群集管理终结点。 然后，管理客户端确定它在与真正的群集通信。 此证书还通过 HTTPS 为 HTTPS 管理 API 和 Service Fabric Explorer 提供 [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm)。
必须获取群集的自定义域名。 从证书颁发机构请求获取证书时，证书的使用者名称必须与用于群集的自定义域名匹配。

若要为应用程序配置 SSL，首先需要获取已由 CA 签名的 SSL 证书。 CA 是受信任的第三方，负责颁发证书，以提高 SSL 安全性。 如果尚无 SSL 证书，需要从销售 SSL 证书的公司购买一个。

该证书必须满足 Azure 中的以下 SSL 证书要求：
-   证书必须包含私钥。

-   必须创建适用于密钥交换的证书，并且证书必须可导出到个人信息交换 (.pfx) 文件中。

-   证书的使用者名称必须与用于访问云服务的域名匹配。

    - 获取用于访问云服务的自定义域名。
    - 请求从 CA 获取证书，其中使用者名称与服务的自定义域名匹配。 例如，如果自定义域名为 __contoso__.com，CA 颁发的证书应包含使用者名称 .contoso.com 或 __www__.contoso.com。

    >[!NOTE]
    >无法从 CA 获取 __cloudapp__.net 域的 SSL 证书。
    
-   证书至少必须使用 2,048 位加密。

HTTP 协议不安全，容易受到窥探攻击威胁。 通过 HTTP 传输的数据在 Web 浏览器到 Web 服务器之间或其他终结点之间作为纯文本发送。 攻击者可以拦截和查看通过 HTTP 发送的敏感数据，如信用卡详细信息和帐户登录凭据。 如果数据使用 HTTPS 通过浏览器进行发送或发布，SSL 可确保加密和保护敏感信息，防止其被拦截。

若要详细了解如何使用 SSL 证书，请参阅[为 Azure 应用程序配置 SSL](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate)。

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>将 Azure Service Fabric 与网络隔离和安全功能结合使用
将 [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)用作示例，设置 nodetype 属性值为 3 的安全群集。 使用此模板和网络安全组控制入站和出站网络流量。

此模板为每个虚拟机规模集都提供了一个 NSG，旨在控制规模集的入站和出站流量。 默认情况下，将会把规则配置为允许模板中指定的系统服务和应用程序端口所需的全部流量进出。 请查看这些规则，并根据需要进行任意更改，包括为应用程序添加新规则。

有关详细信息，请参阅 [Azure Service Fabric 的常见网络方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)。

## <a name="set-up-azure-key-vault-for-security"></a>出于安全考虑，设置 Azure Key Vault
Service Fabric 使用证书提供身份验证和加密，从而保护群集及其应用程序。

Service Fabric 使用 X.509 证书保护群集，并提供应用程序安全功能。 Azure Key Vault 用于管理 Azure 中 Service Fabric 群集的[证书](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)。 创建群集的 Azure 资源提供程序从密钥保管库拉取证书。 然后，当群集在 Azure 上部署时，资源提供程序在 VM 上安装这些证书。

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)、Service Fabric 群集与使用这些证书的资源提供程序之间存在证书关系。 在群集创建后，证书关系的相关信息会存储在密钥保管库中。

设置密钥保管库有两个基本步骤：
1. 专门为密钥保管库创建一个资源组。

    建议将密钥保管库置于它自己的资源组中。 此操作有助于防止在其他资源组（如存储组、计算组或包含群集的组）遭到删除后丢失密钥和机密。 包含 Key Vault 的资源组必须与正在使用它的群集位于同一区域。

2. 在新建的资源组中创建密钥保管库。

    必须启用密钥保管库，才能进行部署。 然后，计算资源提供程序可以从保管库获取证书，并将证书安装在 VM 实例上。

若要详细了解如何设置密钥保管库，请参阅 [Azure Key Vault 入门](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)。

## <a name="assign-users-to-roles"></a>将用户分配到角色
创建应用程序以代表群集后，请将用户分配到 Service Fabric 支持的角色，即只读和管理员。可使用 Azure 门户来分配这些角色。

>[!NOTE]
> 若要详细了解如何在 Service Fabric 使用角色，请参阅[适用于 Service Fabric 客户端的基于角色的访问控制](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)。

对于连接到 [Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)的客户端，Azure Service Fabric 支持两种类型的访问控制：基于管理员和用户的访问控制。 群集管理员可以使用访问控制，限制各组用户执行特定的群集操作。 访问控制可提高群集安全性。

## <a name="next-steps"></a>后续步骤
- 设置 Service Fabric [开发环境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)。
- 了解 [Service Fabric 支持选项](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)。
