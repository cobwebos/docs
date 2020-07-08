---
title: Azure 对等互连服务常见问题解答
description: 了解 Microsoft Azure 对等互连服务常见问题解答
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871357"
---
# <a name="peering-service-faq"></a>对等互连服务常见问题解答

本文介绍有关 Azure 对等互连服务连接的常见问题。


**问：谁是目标客户？**

A. 目标客户是使用 internet 作为传输方式连接到 Microsoft 云的企业。

**问：客户是否可以通过多个提供商注册对等互连服务？** 

A. 是的，客户可以在同一区域或不同区域中注册具有多个提供程序的对等互连服务，但不能为相同的前缀。

**问：客户是否可以为每个地理区域的网站选择唯一的 ISP？**

A. 是的，客户可这样做。 选择适合你的业务和运营需求的每个地区的合作伙伴 ISP。

**问：什么是 Microsoft Edge PoP？**

A. 它是 Microsoft 与其他网络互连的物理位置。 在 Microsoft Edge PoP 位置，托管诸如 Azure 前门和 Azure CDN 等服务。 有关详细信息，请参阅 [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features)。

## <a name="peering-service-unique-characteristics"></a>对等互连服务：唯一特性

**问：对等互连服务与普通 internet 访问有何不同？**

A. 已注册 Microsoft 对等互连服务的合作伙伴与 Microsoft 合作，提供与 Microsoft 服务的优化路由和可靠连接。  

**问：对等互连服务与 ExpressRoute 有何不同？**

A. Azure ExpressRoute 是一个或多个客户位置的专用专用连接。 当对等互连服务提供优化的公共连接，并且不支持任何专用连接时，它还为本地 internet 取得突破提供优化连接。

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务的详细信息，请参阅对[等互连服务概述](about.md)。
- 若要查找服务提供商，请参阅对[等互连服务合作伙伴和位置](location-partners.md)。
- 若要加入对等互连服务连接，请参阅[载入对等互连服务](onboarding-model.md)。
- 若要注册对等互连服务连接，请参阅[注册对等连接服务连接-Azure 门户](azure-portal.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。