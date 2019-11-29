---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566215"
---
本文假设

1. 已在本地网络和 Azure 虚拟网络之间建立**站点到站点 VPN** 或 **ExpressRoute** 连接。
2. 用户帐户有权在 Azure 订阅（虚拟机已故障转移到其中）中创建新的虚拟机。
3. 订阅至少有8个核心可用于启动新的进程服务器虚拟机。
4. 可以提供**配置服务器通行短语**。

> [!TIP]
> 确保可以从 Azure 虚拟网络（虚拟机已故障转移到其中）连接配置服务器（在本地运行）的端口 443。
