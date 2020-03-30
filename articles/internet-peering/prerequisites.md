---
title: 设置与 Microsoft 之间的对等互连的先决条件
titleSuffix: Azure
description: 设置与 Microsoft 之间的对等互连的先决条件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775402"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>设置与 Microsoft 之间的对等互连的先决条件

在请求新的对等互连或将旧体对等互连转换为 Azure 资源之前，请确保满足以下先决条件。

## <a name="azure-related-prerequisites"></a>Azure 相关先决条件
* **微软 Azure 帐户：** 如果没有 Microsoft Azure 帐户，请创建 Microsoft [Azure 帐户](https://azure.microsoft.com/free)。 需要有效和活动的 Microsoft Azure 订阅来设置对等互连，因为对等互连建模为 Azure 订阅中的资源。 请务必注意以下几点：
    * 用于设置对等互连的 Azure 资源类型始终免费 Azure 产品，即不收取创建 Azure 帐户、创建订阅或访问 Azure 资源**对等处理**和**对等互连**的费用。 这与您和 Microsoft 之间直接对等互连的对等协议不相混淆，这些术语与我们的对等团队进行了明确讨论。 如果对此有任何疑问，请与[Microsoft 联系。](mailto:peering@microsoft.com)
    * 您可以使用相同的 Azure 订阅访问其他可能是免费的或付费的 Azure 产品或云服务。 当您访问付费产品时，您将产生费用。
    * 如果要创建新的 Azure 帐户和/或订阅，则在试用期间可能有资格获得免费 Azure 积分，而试用 Azure 云服务。 如果有兴趣，请访问[Microsoft Azure 帐户](https://azure.microsoft.com/free)了解更多信息。

* **关联对等 ASN：** 在请求对等互连之前，请先将 ASN 和联系信息与订阅相关联。 按照[将对等体 ASN 到 Azure 订阅](howto-subscription-association-powershell.md)中的说明进行操作。

## <a name="other-prerequisites"></a>其他先决条件
* **对等数据库配置文件：** 同行们有望在[对同侪DB](https://www.peeringdb.com)上拥有完整和最新的配置文件。 我们在注册系统中使用这些信息来验证同行的详细信息，如 NOC 信息、技术联系信息及其在对等设施的存在等。

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改直接对等互连](howto-direct-portal.md)。
* [使用门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)
* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)