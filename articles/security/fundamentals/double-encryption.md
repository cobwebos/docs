---
title: Microsoft Azure 中的双加密
description: 本文介绍 Microsoft Azure 如何为静态数据和传输中的数据提供双精度加密。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227030"
---
# <a name="double-encryption"></a>双重加密
双加密是指启用了两个或更多独立加密层，以防止任何一层加密的损害。 使用两个加密层可减少对数据加密所带来的威胁。 例如：

- 数据加密中的配置错误
- 加密算法中的实现错误
- 泄露单个加密密钥

Azure 为静态数据和传输中的数据提供双精度加密。

## <a name="data-at-rest"></a>静态数据
Microsoft 针对静态数据启用两层加密的方法是：

- **使用客户托管密钥的磁盘加密**。 提供自己的密钥用于磁盘加密。 你可以将自己的密钥带到 Key Vault (BYOK –创建自己的密钥) ，或在 Azure Key Vault 中生成新的密钥来加密所需的资源。
- **使用平台托管密钥的基础结构加密**。  默认情况下，使用平台托管的加密密钥自动对磁盘进行静态加密。

## <a name="data-in-transit"></a>传输中的数据
对于传输中的数据启用两层加密的方法是：

- **传输加密使用传输层安全性 (TLS) 1.2，用于在云服务和你之间传输数据时保护数据**。 即使流量目标是同一区域中的另一个域控制器，离开数据中心的所有流量仍会在传输过程中加密。 TLS 1.2 是使用的默认安全协议。 TLS 提供严格的身份验证，消息隐私性和完整性强（允许检测消息篡改、拦截和伪造），具有良好的互操作性，算法灵活，易于部署和使用。
- **基础结构层提供的其他加密层**。 使用 IEEE 802.1 AE MAC 安全标准的数据链路层加密方法 (也称为 MACsec) 在基础网络硬件之间通过点到点应用。 每当 Azure 客户流量在数据中心之间移动时（不是由 Microsoft (或代表 Microsoft) 控制），包在发送之前将在设备上加密和解密，从而阻止物理 "中间人" 或侦听/wiretapping 攻击。 由于此技术在网络硬件本身上集成，因此它会在网络硬件上提供线路速率加密，而不会增加可度量的链路延迟。 默认情况下，此 MACsec 加密对于某个区域内或各区域之间的所有 Azure 流量都处于启用状态，并且在要启用的客户部件上无需执行任何操作。

## <a name="next-steps"></a>后续步骤
了解如何 [在 Azure 中使用加密](encryption-overview.md)。
