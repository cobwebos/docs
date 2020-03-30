---
title: 使用门户在直接对等互连上启用对等互连服务
titleSuffix: Azure
description: 使用门户在直接对等互连上启用对等互连服务
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129964"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>使用门户在直接对等互连上启用对等互连服务

本文介绍如何使用门户在直接对等互连上启用[对等互连服务](overview-peering-service.md)。

如果您愿意，您可以使用[PowerShell](howto-peering-service-powershell.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)。
* 选择要启用对等服务的订阅中的直接对等互连。 如果没有，请转换旧版直接对等互连或创建新的直接对等互连。
    * 要转换旧版直接对等互连，请按照将[旧版直接对等互连转换为 Azure 资源的说明使用门户](howto-legacy-direct-portal.md)。
    * 要创建新的直接对等互连，请按照[创建或修改使用门户中的直接对等互连中的](howto-direct-portal.md)说明。

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接对等互连上启用对等互连服务

### <a name="view-direct-peering"></a><a name= get></a>查看直接对等互连
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>为对等互连服务启用直接对等互连

在上一步骤中打开直接对等互连后，将其启用到对等服务。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接对等互连连接

如果需要修改连接设置，请参阅在"创建 **"中修改直接对等部分**[，或使用门户修改直接对等互连](howto-direct-portal.md)

## <a name="next-steps"></a>后续步骤

* [使用门户创建或修改 Exchange 对等互连](howto-exchange-portal.md)
* [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他资源

有关常见问题，请参阅[对等服务常见问题解答](service-faqs.md)。