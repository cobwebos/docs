---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: a20311a0285bf8fda5498241a60b85093039ad19
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513252"
---
## <a name="preventative"></a>预防

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（如客户端、始终加密等）</ul>| 是 | 客户拥有群集以及作为群集构建基础的虚拟机 (VM) 规模集。 可以在 VM 规模集上启用 Azure 磁盘加密。 |
| 传输中加密：<ul><li>快速路由加密</li><li>Vnet 中加密</li><li>VNet-VNet 加密</ul>| 是 |  |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户拥有群集以及作为群集构建基础的虚拟机 (VM) 规模集。 可以在 VM 规模集上启用 Azure 磁盘加密。 |
| 列级加密（Azure 数据服务）| 不适用 |  |
| 加密的 API 调用| 是 | Service Fabric API 调用通过 Azure 资源管理器进行。 需要有效 JSON web 令牌 (JWT)。 |

## <a name="network-segmentation"></a>网络分段

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| vNET 注入支持| 是 |  |
| 网络隔离/防火墙支持| 是 | 使用网络安全组 (NSG)。 |
| 支持强制隧道 | 是 | Azure 网络支持强制隧道。 |

## <a name="detection"></a>检测

| 安全特性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（日志分析、App Insights 等）| 是 | 使用 Azure 监视支持和第三方支持。 |

## <a name="iam-support"></a>IAM 支持

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 访问管理 - 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| 访问管理 - 授权| 是 | 通过 SFRP 进行针对调用的标识和访问管理 (IAM)。 直接对群集终结点进行的调用支持两个角色：用户和管理员。客户可以将 API 映射到任一角色。 |


## <a name="audit-trail"></a>审核线索

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 控制/管理计划日志记录和审核| 是 | 所有控制平面操作都需经过审核和审批流程。 |
| 数据平面日志记录和审核| 不适用 | 客户拥有群集。  |

## <a name="configuration-management"></a>配置管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 服务配置使用 Azure 部署进行版本控制和部署。 代码（应用程序和运行时）使用 Azure 生成进行版本控制。
 |