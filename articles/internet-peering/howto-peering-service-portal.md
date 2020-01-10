---
title: 使用门户通过直接对等互连启用对等互连服务
titleSuffix: Azure
description: 使用门户通过直接对等互连启用对等互连服务
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774973"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>使用门户通过直接对等互连启用对等互连服务

本文介绍如何使用门户通过直接对等互连启用对[等互连服务](overview-peering-service.md)。

如果愿意，可以使用[PowerShell](howto-peering-service-powershell.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)。
* 在订阅中选择要在其上启用对等互连服务的直接对等互连。 如果没有，请转换旧直接对等互连，或创建新的直接对等互连。
    * 若要转换旧版直接对等互连，请按照[使用门户将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-portal.md)中的说明进行操作。
    * 若要创建新的直接对等互连，请按照[使用门户创建或修改直接对等互连](howto-direct-portal.md)中的说明进行操作。

## <a name="enable-peering-service-on-a-direct-peering"></a>对直接对等互连启用对等互连服务

### <a name= get></a>查看直接对等
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>为对等互连服务启用直接对等互连

在上一步中打开直接对等互连后，为对等互连服务启用它。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接对等连接

如果需要修改连接设置，请参阅[使用门户创建或修改直接](howto-direct-portal.md)对等互连中的**修改直接对等**互连部分

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他资源

有关常见问题，请参阅对[等互连服务常见问题解答](service-faqs.md)。