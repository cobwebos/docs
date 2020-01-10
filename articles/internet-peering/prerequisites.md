---
title: 与 Microsoft 建立对等互连的先决条件
titleSuffix: Azure
description: 与 Microsoft 建立对等互连的先决条件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775402"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>与 Microsoft 建立对等互连的先决条件

在请求新的对等互连或将旧对等互连转换为 Azure 资源之前，请确保满足以下先决条件。

## <a name="azure-related-prerequisites"></a>Azure 相关的先决条件
* **Microsoft Azure 帐户：** 如果没有 Microsoft Azure 帐户，请创建[Microsoft Azure 帐户](https://azure.microsoft.com/free)。 若要设置对等互连，则需要有效的和活动 Microsoft Azure 订阅，因为对等互连将建模为 Azure 订阅中的资源。 请务必注意以下几点：
    * 用于设置对等互连的 Azure 资源类型始终为免费的 Azure 产品，即，你不需要创建 Azure 帐户或创建订阅或访问 Azure 资源**PeerAsn**和对等**互连**来设置对等互连。 这不会与你和 Microsoft 之间的直接对等互连协议混淆，这些条款与我们的对等团队明确讨论。 如果此方面有任何问题，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。
    * 你可以使用相同的 Azure 订阅来访问其他 Azure 产品或可能免费或付费的云服务。 访问付费产品时，会产生费用。
    * 如果要创建新的 Azure 帐户和/或订阅，可以在试用期间使用免费的 Azure 信用额度，以便试用 Azure 云服务。 如果感兴趣，请访问[Microsoft Azure 帐户](https://azure.microsoft.com/free)获取详细信息。

* **关联对等 ASN：** 请求对等互连之前，请先将你的 ASN 和联系人信息关联到你的订阅。 按照[将对等 ASN 连接到 Azure 订阅](howto-subscription-association-powershell.md)中的说明进行操作。

## <a name="other-prerequisites"></a>其他先决条件
* **PeeringDB 配置文件：** 对等端应在[PeeringDB](https://www.peeringdb.com)上具有完整的和最新的配置文件。 我们使用注册系统中的此信息来验证对等方的详细信息，例如 NOC 信息、技术联系人信息，以及它们在对等互连设施上的存在性等。

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)。
* [使用门户将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)
* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)