---
title: Azure IoT Edge 模块必备组件 |Azure Marketplace
description: 发布 IoT Edge 模块的先决条件。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142372"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge 模块发布先决条件

>[!Important]
>从2020年4月13日开始，我们将开始向合作伙伴中心移动 IoT Edge 模块的管理。 迁移后，你将在合作伙伴中心创建和管理你的产品/服务。 按照[创建 IoT Edge 模块提供](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)中的说明来管理迁移的产品/服务。

本文介绍了发布新 IoT Edge 模块产品/服务的先决条件。  如果尚未执行此操作，请查看[IoT Edge 模块发布指南](../..//iot-edge-module.md)。


## <a name="publishing-prerequisites"></a>发布先决条件

若要将 IoT Edge 模块发布到 Azure 市场，必须满足以下先决条件：

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- 对[云合作伙伴门户](https://cloudpartner.azure.com/)的访问权限。 有关详细信息，请参阅 [Azure 市场和 AppSource 发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- [Azure Marketplace 条款](https://azure.microsoft.com/support/legal/marketplace-terms/)协议
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

[准备好 IoT Edge 模块技术资产](./cpp-create-technical-assets.md)后，便可以[创建 IoT Edge 模块产品](./cpp-create-offer.md)。 
