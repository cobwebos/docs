---
title: Azure IoT Edge 模块先决条件 | Microsoft Docs
description: 发布 IoT Edge 模块的先决条件。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432436"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 模块发布先决条件

本文介绍了发布新 IoT Edge 模块产品/服务的先决条件。

若要详细了解 IoT Edge 模块以及将模块发布到 Azure 市场的好处，请参阅 [IoT Edge 模块发布指南](https://docs.microsoft.com/azure/marketplace/iot-edge-module)。

## <a name="publishing-prerequisites"></a>发布先决条件

若要将 IoT Edge 模块发布到 Azure 市场，必须满足以下先决条件：

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- 具有对[云合作伙伴门户](https://cloudpartner.azure.com/)的访问权限。 有关详细信息，请参阅 [Azure 市场和 AppSource 发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 同意 [Azure 市场条款](https://azure.microsoft.com/support/legal/marketplace-terms/)
- 将 IoT Edge 模块技术资产承载在 Azure 容器注册表中。  有关详细信息，请参阅[如何准备你的 IoT Edge 模块技术资产](./cpp-create-technical-assets.md)
- 使你的 IoT Edge 模块元数据就绪可用。 例如（非详尽列表）：
    - 标题
    - 说明（采用 HTML 格式）
    - 徽标图像（PNG 格式和包括 40x40px、90x90px、115x115px、255x115px 的固定图像大小）
    - 使用条款和隐私策略
    - 包括以下项的默认模块配置（路由、孪生所需属性、createOptions 和环境变量）。
    - 文档
    - 支持联系人

## <a name="next-steps"></a>后续步骤

- [准备 IoT Edge 模块技术资产](./cpp-create-technical-assets.md)
- [创建 IoT Edge 模块产品/服务](./cpp-create-offer.md)
