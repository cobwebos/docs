---
title: 管理 Azure IoT 中心的公用网络访问
description: 有关如何禁用和启用 IoT 中心的公用网络访问的文档
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937489"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>管理 IoT 中心的公用网络访问

若要仅允许访问 [VNet 中 IoT 中心的专用终结点](virtual-network-support.md)，请禁用公共网络访问。 为此，请使用 Azure 门户或 `publicNetworkAccess` API。 

## <a name="turn-off-public-network-access-using-azure-portal"></a>使用 Azure 门户禁用公用网络访问

1. 访问 [Azure 门户](https://portal.azure.com)
2. 导航到 IoT 中心。
3. 从左侧菜单中选择“网络”。
4. 在“允许公用网络访问”下，选择“已禁用”
5. 选择“保存” 。

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="显示在 Azure 门户中的何处禁用公用网络访问的图像" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

若要启用公用网络访问，请选择“已启用”，然后选择“保存”。

## <a name="ip-filter"></a>筛选器 

如果禁用了公用网络访问，则会忽略所有 [IP 筛选器](iot-hub-ip-filtering.md)规则。 这是因为系统会阻止公共 Internet 中的所有 IP。 若要使用 IP 筛选器，请使用“所选 IP 范围”选项。

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>与事件中心兼容的内置终结点的 bug 修复

IoT 中心有一个 bug，即，禁用对 IoT 中心的公用网络访问后，仍可通过公共 Internet 访问[与事件中心兼容的内置终结点](iot-hub-devguide-messages-read-builtin.md)。 若要了解有关此 bug 的详细信息，并与我们联系以解决此 bug，请参阅[如果禁用 IoT 中心的公用网络访问，将禁止访问内置事件中心终结点](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)。