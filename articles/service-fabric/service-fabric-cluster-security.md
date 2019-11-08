---
title: 保护 Azure Service Fabric 群集 | Microsoft Docs
description: 了解有关 Azure Service Fabric 群集的安全性方案，以及用于实现它们的各种技术。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: atsenthi
ms.openlocfilehash: cf808bef75a73cef6e8c17045506f29fabf3b52e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819446"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric 群集安全方案
Azure Service Fabric 群集是你拥有的资源。 你应保护群集，防止未经授权的用户与其连接。 当在群集上运行生产工作负荷时，安全的群集环境尤为重要。 可以创建不安全的群集，但是，如果群集向公共 internet 公开管理终结点，则匿名用户可以连接到该群集。 生产工作负荷不支持不安全群集。 

本文概述了适用于 Azure 群集和独立群集的安全性方案，以及用于实现它们的各种技术：

* 节点到节点安全性
* 客户端到节点安全性
* 基于角色的访问控制 (RBAC)

## <a name="node-to-node-security"></a>节点到节点安全性
节点到节点安全性可保护群集中 VM 或计算机之间的通信。 这种安全性方案确保只有已获授权加入群集的计算机可以参与到托管群集中的应用程序和服务。

![节点到节点通信示意图][Node-to-Node]

在 Azure 上运行的群集和在 Windows 上运行的独立群集可以使用[证书安全性](https://msdn.microsoft.com/library/ff649801.aspx)或适用于 Windows Server 计算机的 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

### <a name="node-to-node-certificate-security"></a>节点到节点的证书安全性
创建群集时，Service Fabric 将使用指定为节点类型配置一部分的 X.509 服务器证书。 本文末尾概述了这些证书是什么，以及如何获取或创建这些证书。

证书安全性是在通过 Azure 门户、Azure 资源管理器模板或独立的 JSON 模板创建群集时配置的。 Service Fabric SDK 的默认行为是部署并安装距未来过期证书最远的证书；经典行为允许定义主要证书和次要证书，允许手动发起的变换，故不推荐将其用于新功能之上。 将要使用的距离未来到期日期最远的主证书应与为[客户端到节点安全性](#client-to-node-security)设置的管理员客户端和只读客户端证书不同。

若要了解如何在群集中为 Azure 设置证书安全性，请参阅[使用 Azure 资源管理器模板设置群集](service-fabric-cluster-creation-via-arm.md)。

若要了解如何在群集中为独立 Windows Server 群集设置证书安全性，请参阅[通过使用 X.509 证书在 Windows 上保护独立群集](service-fabric-windows-cluster-x509-security.md)。

### <a name="node-to-node-windows-security"></a>节点到节点的 Windows 安全性
若要了解如何为独立 Windows Server 群集设置 Windows 安全性，请参阅[通过使用 Windows 安全性在 Windows 上保护独立群集](service-fabric-windows-cluster-windows-security.md)。

## <a name="client-to-node-security"></a>客户端到节点安全性
客户端到节点安全性对客户端进行身份验证，并保护客户端与群集中单个节点之间的通信。 这种类型的安全性确保只有已获授权的用户可以访问群集与群集上部署的应用程序。 客户端通过其 Windows 安全性凭据或其证书安全性凭据进行唯一标识。

![客户端到节点通信示意图][Client-to-Node]

在 Azure 上运行的群集和在 Windows 上运行的独立群集可以使用[证书安全性](https://msdn.microsoft.com/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

### <a name="client-to-node-certificate-security"></a>客户端到节点的证书安全性
客户端到节点证书安全性是在通过 Azure 门户、资源管理器模板或独立的 JSON 模板创建群集时设置的。 要创建证书，请指定管理员客户端证书或用户客户端证书。 作为最佳做法，指定的管理员客户端证书和用户客户端证书应该不同于为[节点到节点安全性](#node-to-node-security)指定的主证书和辅助证书。 群集证书与客户端管理员证书具有相同的权限。 但是，它们只应由群集使用，而不应作为最佳安全方案使用。

客户端如果使用管理员证书连接到群集，则拥有管理功能的完全访问权限。 客户端如果使用只读的用户客户端证书连接到群集，则只拥有管理功能的只读访问权限。 这些证书用于本文稍后介绍的 RBAC。

若要了解如何在群集中为 Azure 设置证书安全性，请参阅[使用 Azure 资源管理器模板设置群集](service-fabric-cluster-creation-via-arm.md)。

若要了解如何在群集中为独立 Windows Server 群集设置证书安全性，请参阅[通过使用 X.509 证书在 Windows 上保护独立群集](service-fabric-windows-cluster-x509-security.md)。

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure 上客户端到节点的 Azure Active Directory 安全性
通过 Azure AD，组织（称为租户）可管理用户对应用程序的访问。 应用程序分为采用基于 Web 的登录 UI 的应用程序和采用本地客户端体验的应用程序。 如果尚未创建租户，请先阅读[如何获取 Azure Active Directory 租户][active-directory-howto-tenant]。

Service Fabric 群集提供其管理功能的各种入口点，包括基于 Web 的 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 和 [Visual Studio][service-fabric-manage-application-in-visual-studio]。 因此，需要创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。

对在 Azure 上运行的群集，也可使用 Azure Active Directory (Azure AD) 来保护对管理终结点的访问。 若要了解如何创建所需的 Azure AD 项目以及如何在创建群集时填充项目，请参阅[设置 Azure AD 以对客户端进行身份验证](service-fabric-cluster-creation-setup-aad.md)。

## <a name="security-recommendations"></a>安全建议
如果将 Service Fabric 群集部署在某个公共网络中，而该网络托管在 Azure 上，则对于客户端到节点型相互身份验证，建议如下：
*   对客户端标识使用 Azure Active Directory
*   对服务器标识使用证书，并对 http 通信进行 SSL 加密

如果将 Service Fabric 群集部署在某个公共网络中，而该网络托管在 Azure 上，则对于节点到节点安全，建议使用群集证书对节点进行身份验证。 


对于独立 Windows Server 群集，如果有 Windows Server 2012 R2 和 Windows Active Directory，建议结合使用 Windows 安全性和组托管服务帐户。 否则，可以结合使用 Windows 安全性和 Windows 帐户。

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)
可以使用访问控制限制对不同用户组的某些群集操作的访问。 这就使得群集更加安全。 连接到群集的客户端支持两种访问控制类型：管理员角色和用户角色。

被分配为管理员角色的用户对管理功能（包括读取和写入功能）拥有完全访问权限。 默认情况下，被分配为用户角色的用户只有管理功能的读取访问权限（例如查询功能）， 以及解析应用程序和服务的能力。

创建群集时，请设置管理员和用户客户端角色。 通过提供单独的标识（例如使用证书或 Azure AD），为每种角色类型分配角色。 若要详细了解默认访问控制设置以及如何更改默认设置，请参阅 [Service Fabric 客户端的基于角色的访问控制](service-fabric-cluster-security-roles.md)。

## <a name="x509-certificates-and-service-fabric"></a>X.509 证书和 Service Fabric
X.509 数字证书通常用于验证客户端与服务器。 它们还用于对消息进行加密和数字签名。 Service Fabric 使用 X.509 证书保护群集，提供应用程序安全功能。 有关 X.509 数字证书的详细信息，请参阅[使用证书](https://msdn.microsoft.com/library/ms731899.aspx)。 可以使用 [Key Vault](../key-vault/key-vault-overview.md) 管理 Azure 中 Service Fabric 群集的证书。

要考虑的几个要点：

* 要为运行生产工作负荷的群集创建证书，请使用正确配置的 Windows Server 证书服务，或从已批准的[证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 获取。
* 请勿在生产环境中使用任何由 MakeCert.exe 等工具创建的临时或测试证书。
* 可使用自签名证书，但仅限在测试群集中使用。 请勿在生产中使用自签名证书。
* 生成证书指纹时，请确保生成 SHA1 指纹。 在配置客户端和群集证书指纹时，使用的是 SHA1。

### <a name="cluster-and-server-certificate-required"></a>群集和服务器证书（必需）
必须使用这些证书（一个主要证书，以及一个可选的辅助证书）来保护群集，并防止未经授权的访问。 这些证书提供了群集和服务器身份验证。

群集身份验证在群集联合的情况下对节点间的通信进行身份验证。 只有可以使用此证书自我证明身份的节点才能加入群集。 服务器身份验证在管理客户端上对群集管理终结点进行身份验证，使管理客户端知道它正在与真正的群集而不是“中间人”通信。 此证书还通过 HTTPS 为 HTTPS 管理 API 和 Service Fabric Explorer 提供 SSL。 客户端或节点对节点进行身份验证时，一项初始检查是检查“使用者”字段中的公用名值。 此公用名或某个证书的使用者可选名称 (SAN) 必须存在于允许的公用名列表中。

该证书必须满足以下要求：

* 证书必须包含私钥。 这些证书通常使用扩展名 .pfx 或 .pem  
* 必须为密钥交换创建证书，并且该证书可导出到个人信息交换 (.pfx) 文件。
* **证书的使用者名称必须与用于访问 Service Fabric 群集的域匹配**。 只有满足此匹配，才能为群集的 HTTPS 管理终结点和 Service Fabric Explorer 提供 SSL。 无法从证书颁发机构 (CA) 处获取针对 *.cloudapp.azure.com 域的 SSL 证书。 必须获取群集的自定义域名。 从 CA 请求证书时，该证书的使用者名称必须与用于群集的自定义域名匹配。

其他注意事项：

* “使用者”字段可有多个值。 每个值都以名称首字母为前缀，指示值的类型。 常见的名称首字母是“CN”（表示公用名），例如 **CN = www**contoso.com **\.** 。 
* “使用者”字段可为空。 
* 如果可选的“使用者可选名称”字段已填充数据，则此字段必须包含证书的公用名，以及每个 SAN 的一个条目。 这些内容作为“DNS 名称”值输入。 要了解如何生成具有 SAN 的证书，请参阅[如何向安全 LDAP 证书添加使用者可选名称](https://support.microsoft.com/kb/931351)。
* 证书的“预期目的”字段值应包含适当的值，例如“服务器身份验证”或“客户端身份验证”。

### <a name="application-certificates-optional"></a>应用程序证书（可选）
可以出于应用程序安全目的，在群集上安装任意数量的附加证书。 在创建群集之前，请考虑需要在节点上安装证书的应用程序安全方案，例如：

* 加密和解密应用程序配置值。
* 在复制期间跨节点加密数据。

无论是 Linux 或 Windows 群集，创建安全群集的概念是相同的。

### <a name="client-authentication-certificates-optional"></a>客户端身份验证证书（可选）
可以指定任意数量的其他证书用于管理员客户端操作或用户客户端操作。 客户端可以在需要相互身份验证时使用此类证书。 客户端证书通常不由第三方 CA 颁发。 当前用户位置的“个人”存储通常包含由根证书颁发机构放置的客户端证书。 此证书的“预期目的”值应为“客户端身份验证”。  

默认情况下，群集证书具有管理客户端的特权。 这些其他客户端证书不应安装到集群中，而应被指定为允许在群集配置中使用。  但是，客户端证书需要安装在客户端计算机上，以便连接到群集并执行操作。

> [!NOTE]
> Service Fabric 群集上的所有管理操作都需要服务器证书。 客户端证书不能用于管理。

## <a name="next-steps"></a>后续步骤
* [使用资源管理器模板在 Azure 中创建群集](service-fabric-cluster-creation-via-arm.md) 
* [使用 Azure 门户创建群集](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
