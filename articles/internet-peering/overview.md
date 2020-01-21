---
title: 设置与 Microsoft 的对等互连
titleSuffix: Azure
description: 对等互连概述
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75908918"
---
# <a name="internet-peering-overview"></a>Internet 对等互连概述

对等互连是 Microsoft 的全球网络 (AS8075) 和你的网络之间的互连，目的是在 Microsoft 联机服务和 Microsoft Azure 服务之间交换 Internet 流量。 运营商或服务提供商可以在我们的任何边缘位置请求连接到 Microsoft。 Microsoft 对每个请求进行审核，确保它遵守我们的对等互连策略。 可以通过两种方式设置与 Microsoft 网络的对等互连：

* **直接对等互连：**

    对等互连通过 Microsoft Edge 上的 Microsoft 网络和你的网络之间的直接物理连接建立。 BGP 会话是按路由策略并使用预协商协议跨这些连接配置的。 这也称为 PNI。

* **Exchange 对等互连：**

    这是指 Internet 交换 (IX) 上的标准公共对等互连。 Microsoft 网络与你的网络之间的物理连接通过 IX 操作的交换机构造来完成。 使用 IX 提供的 IP 空间配置 BGP 会话。

## <a name="benefits-of-peering-with-microsoft"></a>与 Microsoft 进行对等互连的优点
* 通过与 Microsoft 的对等互连提供 Microsoft 流量，以便降低传输成本。
* 通过减少网络跃点并降低 Microsoft Edge 网络的延迟，为客户提高性能。
* 通过与 Microsoft 在冗余位置进行对等互连，防止客户流量受你的网络或传输提供商的网络故障的影响。
* 了解有关对等互连连接的性能指标，并利用见解对网络进行故障排除。

## <a name="benefits-of-using-azure-to-set-up-peering"></a>使用 Azure 设置对等互连的优点

可以使用 Azure PowerShell 或门户请求与 Microsoft 对等互连。 以这种方式设置的对等互连作为 Azure 资源进行管理，具有以下优势：
* 用于设置和管理与 Microsoft 的对等互连的步骤得到了简化并实现了自动化。
* 可以在一个位置查看和管理所有对等互连，既快速又简单。
* 跟踪所有连接的状态和带宽数据。
* 可以使用同一订阅访问 Azure 云服务。

如果已与 Microsoft 建立了对等互连，则称之为**传统对等互连**。 可以选择将此类对等互连作为 Azure 资源进行管理，以便充分利用上述优势。 若要提交新的对等互连请求，或将旧对等互连转换为 Azure 资源，请按照下面的“后续步骤”  部分的链接进行操作。

## <a name="peering-policy"></a>对等互连策略
Microsoft 有一项选择性的但通常处于打开状态的对等互连策略。 对等方的选择取决于性能和功能，其中有互利因素，并受特定的技术、商业和法律要求的限制。 有关详细信息，请参阅[对等互连策略](policy.md)。

## <a name="faq"></a>常见问题解答
有关对等互连的常见问题解答，请参阅 [Internet 对等互连 - 常见问题解答](faqs.md)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何通过相关步骤设置与 Microsoft 的直接对等互连，请按[直接对等互连演练](walkthrough-direct-all.md)进行操作
* 若要了解如何通过相关步骤设置与 Microsoft 的 Exchange 对等互连，请按 [Exchange 对等互连演练](walkthrough-exchange-all.md)进行操作
* 了解 Azure 的部分其他关键[网络功能](https://docs.microsoft.com/azure/networking/networking-overview)。
