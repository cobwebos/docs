---
title: 管理 Azure IoT 中心的公共网络访问
description: 有关如何为 IoT 中心禁用和启用公用网络访问的文档
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937489"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>管理 IoT 中心的公共网络访问

若要仅允许访问[VNet 中 IoT 中心的专用终结点](virtual-network-support.md)，请禁用公共网络访问。 为此，请使用 Azure 门户或 `publicNetworkAccess` API。 

## <a name="turn-off-public-network-access-using-azure-portal"></a>使用 Azure 门户关闭公共网络访问

1. 访问[Azure 门户](https://portal.azure.com)
2. 导航到 IoT 中心。
3. 从左侧菜单中选择 "**网络**"。
4. 在 "允许对其进行公用网络访问" 下，选择 "**禁用**"
5. 选择“保存”。

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="显示 Azure 门户关闭公共网络访问的位置的图像" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

若要启用公共网络访问权限，请选择 "**已启用**"，然后选择 "**保存**"。

## <a name="ip-filter"></a>筛选器 

如果禁用公共网络访问，则忽略所有[IP 筛选器](iot-hub-ip-filtering.md)规则。 这是因为公共 internet 中的所有 Ip 均被阻止。 若要使用 IP 筛选器，请使用 "**所选 IP 范围**" 选项。

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>通过内置的与事件中心兼容的终结点修复 Bug

IoT 中心存在 bug，在禁用对 IoT 中心的公共网络访问权限时，可继续通过公共 internet 访问[内置的事件中心兼容终结点](iot-hub-devguide-messages-read-builtin.md)。 若要了解详细信息并与我们联系了解此错误，请参阅[禁用 IoT 中心的公共网络访问禁用对内置事件中心终结点的访问](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)。