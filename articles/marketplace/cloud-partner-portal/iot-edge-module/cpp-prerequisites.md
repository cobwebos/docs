---
title: Azure IoT 边缘模块先决条件 |Azure 应用商店
description: 发布 IoT Edge 模块的先决条件。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b6e021fc452b45edd7b1be9fd5afd77b792b4853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286534"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 模块发布先决条件

本文介绍了发布新 IoT Edge 模块产品/服务的先决条件。  如果尚未这样做，请查看[IoT Edge 模块发布指南](../..//iot-edge-module.md)。


## <a name="publishing-prerequisites"></a>发布先决条件

若要将 IoT Edge 模块发布到 Azure 市场，必须满足以下先决条件：

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- 访问[云合作伙伴门户](https://cloudpartner.azure.com/)。 有关详细信息，请参阅 [Azure 市场和 AppSource 发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 与[Azure 应用商店条款](https://azure.microsoft.com/support/legal/marketplace-terms/)的协议
- 将 IoT Edge 模块技术资产承载在 Azure 容器注册表中。  有关详细信息，请参阅[如何准备你的 IoT Edge 模块技术资产](./cpp-create-technical-assets.md)
- 使你的 IoT Edge 模块元数据就绪可用。 例如，准备以下资产：
    - 标题
    - 说明（采用 HTML 格式）
    - 徽标图像（PNG 格式和包括 40x40px、90x90px、115x115px、255x115px 的固定图像大小）
    - 使用条款和隐私策略
    - 包括以下项的默认模块配置（路由、孪生所需属性、createOptions 和环境变量）。
    - 模块文档
    - 支持联系人


## <a name="next-steps"></a>后续步骤

一旦[您准备好 IoT Edge 模块技术资产](./cpp-create-technical-assets.md)，您将准备好[创建 IoT Edge 模块产品](./cpp-create-offer.md)。 
