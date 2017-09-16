---
title: "保护 Azure Service Fabric 群集 | Microsoft Docs"
description: "了解有关 Azure Service Fabric 群集的安全性方案，以及用于实现它们的各种技术。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/06/2017

---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric 群集安全方案
Azure Service Fabric 群集是你拥有的资源。 必须保护群集，防止未经授权的用户与其连接。 当在群集上运行生产工作负荷时，安全的群集环境尤为重要。 尽管可以创建不安全的群集，但当该群集向公共 Internet 公开管理终结点时，匿名用户可与它建立连接。 

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

证书安全性是在通过 Azure 门户、Azure 资源管理器模板或独立的 JSON 模板创建群集时配置的。 可以指定一个主要证书和一个可选的辅助证书（用于证书滚动更新）。 设置的主证书和辅助证书应不同于为[客户端到节点安全性](#client-to-node-security)设置的管理员客户端证书和只读客户端证书。

若要了解如何在群集中为 Azure 设置证书安全性，请参阅[使用 Azure 资源管理器模板设置群集](service-fabric-cluster-creation-via-arm.md)。

若要了解如何在群集中为独立 Windows Server 群集设置证书安全性，请参阅[通过使用 X.509 证书在 Windows 上保护独立群集](service-fabric-windows-cluster-x509-security.md)。

### <a name="node-to-node-windows-security"></a>节点到节点的 Windows 安全性
若要了解如何为独立 Windows Server 群集设置 Windows 安全性，请参阅[通过使用 Windows 安全性在 Windows 上保护独立群集](service-fabric-windows-cluster-windows-security.md)。

## <a name="client-to-node-security"></a>客户端到节点安全性
客户端到节点安全性对客户端进行身份验证，并保护客户端与群集中单个节点之间的通信。 这种类型的安全性确保只有已获授权的用户可以访问群集与群集上部署的应用程序。 客户端通过其 Windows 安全性凭据或其证书安全性凭据进行唯一标识。

![客户端到节点通信示意图][Client-to-Node]

在 Azure 上运行的群集和在 Windows 上运行的独立群集可以使用[证书安全性](https://msdn.microsoft.com/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

### <a name="client-to-node-certificate-security"></a>客户端到节点的证书安全性
客户端到节点证书安全性是在通过 Azure 门户、资源管理器模板或独立的 JSON 模板创建群集时设置的。 要创建证书，请指定管理员客户端证书或用户客户端证书。 作为最佳做法，指定的管理员客户端证书和用户客户端证书应该不同于为[节点到节点安全性](#node-to-node-security)指定的主证书和辅助证书。 默认情况下，用于实现节点到节点安全性的群集证书会添加到允许的客户端管理员证书列表中。

客户端如果使用管理员证书连接到群集，则拥有管理功能的完全访问权限。 客户端如果使用只读的用户客户端证书连接到群集，则只拥有管理功能的只读访问权限。 这些证书用于我们将在本文后面介绍的 RBAC。

若要了解如何在群集中为 Azure 设置证书安全性，请参阅[使用 Azure 资源管理器模板设置群集](service-fabric-cluster-creation-via-arm.md)。

若要了解如何在群集中为独立 Windows Server 群集设置证书安全性，请参阅[通过使用 X.509 证书在 Windows 上保护独立群集](service-fabric-windows-cluster-x509-security.md)。

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure 上客户端到节点的 Azure Active Directory 安全性
对在 Azure 上运行的群集，也可使用 Azure Active Directory (Azure AD) 来保护对管理终结点的访问。 若要了解如何创建必要的 Azure AD 项目、如何在创建群集期间填充这些项目，以及之后如何连接这些群集，请参阅[使用 Azure 资源管理器模板设置群集](service-fabric-cluster-creation-via-arm.md)。

## <a name="security-recommendations"></a>安全建议
对于 Azure 群集，建议针对节点到节点安全性使用 Azure AD 安全性来验证客户端和证书。

对于独立 Windows Server 群集，如果有 Windows Server 2012 R2 和 Windows Active Directory，建议结合使用 Windows 安全性和组托管服务帐户。 否则，可以结合使用 Windows 安全性和 Windows 帐户。

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)
可以使用访问控制限制对不同用户组的某些群集操作的访问。 这就使得群集更加安全。 连接到群集的客户端支持两种访问控制类型：管理员角色和用户角色。

被分配为管理员角色的用户对管理功能（包括读取和写入功能）拥有完全访问权限。 默认情况下，被分配为用户角色的用户只有管理功能的读取访问权限（例如查询功能）， 以及解析应用程序和服务的能力。

创建群集时，请设置管理员和用户客户端角色。 通过提供单独的标识（例如使用证书或 Azure AD），为每种角色类型分配角色。 若要详细了解默认访问控制设置以及如何更改默认设置，请参阅 [Service Fabric 客户端的基于角色的访问控制](service-fabric-cluster-security-roles.md)。

## <a name="x509-certificates-and-service-fabric"></a>X.509 证书和 Service Fabric
X.509 数字证书通常用于验证客户端与服务器。 它们还用于对消息进行加密和数字签名。 有关 X.509 数字证书的详细信息，请参阅[使用证书](http://msdn.microsoft.com/library/ms731899.aspx)。

要考虑的几个要点：

* 要为运行生产工作负荷的群集创建证书，请使用正确配置的 Windows Server 证书服务，或从已批准的[证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 获取。
* 请勿在生产环境中使用任何由 MakeCert.exe 等工具创建的临时或测试证书。
* 可使用自签名证书，但仅限在测试群集中使用。 请勿在生产中使用自签名证书。

### <a name="server-x509-certificates"></a>服务器 X.509 证书
服务器证书的主要任务是在客户端上对服务器（节点）进行身份验证，或者在一个服务器（节点）上对另一个服务器（节点）进行身份验证。 客户端或节点对节点进行身份验证时，一项初始检查是检查“使用者”字段中的公用名值。 此公用名或某个证书的使用者可选名称 (SAN) 必须存在于允许的公用名列表中。

要了解如何生成具有 SAN 的证书，请参阅[如何向安全 LDAP 证书添加使用者可选名称](http://support.microsoft.com/kb/931351)。

“使用者”字段可有多个值。 每个值都以名称首字母为前缀，指示值的类型。 常见的名称首字母是“CN”（表示公用名），例如 CN = www.contoso.com。“使用者”字段可为空。 如果可选的“使用者可选名称”字段已填充数据，则此字段必须包含证书的公用名，以及每个 SAN 的一个条目。 这些内容作为“DNS 名称”值输入。

证书的“预期目的”字段值应包含适当的值，例如“服务器身份验证”或“客户端身份验证”。

### <a name="client-x509-certificates"></a>客户端 X.509 证书
客户端证书通常不由第三方 CA 颁发。 相反，当前用户位置的“个人”存储通常包含由根证书颁发机构放置的客户端证书，其“预期目的”值是“客户端身份验证”。 客户端可以在需要相互身份验证时使用此类证书。

> [!NOTE]
> Service Fabric 群集上的所有管理操作都需要服务器证书。 客户端证书不能用于管理。

## <a name="next-steps"></a>后续步骤
* [使用资源管理器模板在 Azure 中创建群集](service-fabric-cluster-creation-via-arm.md) 
* [使用 Azure 门户创建群集](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

