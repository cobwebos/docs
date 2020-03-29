---
title: 对等互连服务合作伙伴演练
titleSuffix: Azure
description: 对等互连服务合作伙伴演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720242"
---
# <a name="peering-service-partner-walkthrough"></a>对等互连服务合作伙伴演练

本节介绍提供程序为启用对等互连服务启用直接对等互连所需的步骤。

## <a name="create-direct-peering-connection-for-peering-service"></a>为对等服务创建直接对等互连连接
服务提供商可以通过创建新的直接对等互连来扩展其地理覆盖范围，以支持对等互连服务。 为此，
1. 成为对等服务合作伙伴（如果尚未）
1. 按照说明使用[门户创建或修改直接对等互连](howto-direct-portal.md)。 确保它满足高可用性要求。
1. 然后，按照以下步骤[使用门户 在直接对等互连上启用对等互连服务](howto-peering-service-portal.md)。

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>使用旧版直接对等互连连接进行对等互连服务
如果您有要用于支持对等互连服务的旧版直接对等互连，
1. 如果尚未成为对等服务合作伙伴，则成为对等服务合作伙伴。
1. 按照说明使用[门户将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)。 如果需要，订购其他电路以满足高可用性要求。
1. 然后，按照以下步骤[使用门户 在直接对等互连上启用对等互连服务](howto-peering-service-portal.md)。

## <a name="next-steps"></a>后续步骤

* 了解[对等策略](https://peering.azurewebsites.net/peering)。
* 要了解设置与 Microsoft 的直接对等互连的步骤，请按照[直接对等演练进行](walkthrough-direct-all.md)。
* 要了解与 Microsoft 设置 Exchange 对等互连的步骤，请按照[Exchange 对等演练进行](walkthrough-exchange-all.md)。