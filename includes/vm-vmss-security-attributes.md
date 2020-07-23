---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 520fd50b2b0864f43c08687f05de377679b36d84
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886880"
---
## <a name="preventative"></a>预防

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 请参阅[如何在 Azure 中加密 Linux 虚拟机](/azure/virtual-machines/linux/encrypt-disks)和[加密 Windows VM 上的虚拟磁盘](/azure/virtual-machines/windows/encrypt-disks)。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | Azure 虚拟机支持[ExpressRoute](/azure/expressroute)和 VNet 加密。 请参阅[Vm 中的传输中加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户托管的密钥是受支持的 Azure 加密方案;请参阅[Azure 加密概述](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过 HTTPS 和 SSL。 |

## <a name="network-segmentation"></a>网络分段

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | |
| VNet 注入支持| 是 | . |
| 网络隔离和防火墙支持| 是 |  |
| 强制隧道支持| 是 | 请参阅[使用 Azure 资源管理器部署模型配置强制隧道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

## <a name="detection"></a>检测

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[在 Azure 中监视和更新 Linux 虚拟机](/azure/virtual-machines/linux/tutorial-monitoring)和[在 Azure 中监视和更新 Windows 虚拟机](/azure/virtual-machines/windows/tutorial-monitoring)。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 |  |
| Authorization| 是 |  |


## <a name="audit-trail"></a>审核线索

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 |  |
| 数据平面日志记录和审核 | 否 |  |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 |  | 
