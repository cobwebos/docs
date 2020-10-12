---
title: Azure 市场 IoT Edge 模块产品/服务
description: 了解如何在 Azure 市场中发布 IoT Edge 模块产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/18/2020
ms.openlocfilehash: 90267f2b75b7b9c1e77a45d7e3faa4b0bf6dd63a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708537"
---
# <a name="iot-edge-modules"></a>IoT Edge 模块

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 平台由 Microsoft Azure 提供支持。  用户可以通过此平台部署云工作负载以直接在 IoT 设备上运行。  IoT Edge 模块可以运行脱机工作负载，并在本地执行数据分析。 此套餐类型可帮助节省带宽、保护本地数据和敏感数据，并提供低延迟响应时间。  现在，你可以选择利用这些预生成的工作负载。 到目前为止，只有少量来自 Microsoft 的第一方解决方案可用。  必须投入时间和资源来构建你自己的自定义 IoT 解决方案。

使用 [Azure 市场中的 IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)，我们现在有单个目标，让发布者向 IoT 受众公开和销售其解决方案。 最终，IoT 开发人员可以查找和购买功能，以便加速其解决方案的开发进程。  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure 市场中 IoT Edge 模块的主要优点

| **对于发布者**    | **对于客户（IoT 开发人员）**  |
| :------------------- | :-------------------|
| 让数百万开发人员着眼于构建和部署 IoT Edge 解决方案。  | 使用经测试的安全组件，满怀信心地构建 IoT Edge 解决方案。 |
| 发布一次并在任何支持容器的 IoT Edge 硬件上运行。 | 通过查找和部署第一方和第三方 IoT Edge 模块来缩短上市时间，以满足特定需求。 |
| 通过灵活的计费选项进行盈利 <ul> <li> 免费和自带许可证 (BYOL)。 </li> </ul> | 使用所选的计费模式购买。 <ul> <li> 免费和自带许可证 (BYOL)。 </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge 模块是什么？

通过 Azure IoT Edge，可让你以模块的形式来部署和管理 Edge 上的业务逻辑。 Azure IoT Edge 模块是 IoT Edge 管理的最小计算单元，可以包含 Microsoft 服务 (如 Azure 流分析) 、第三方服务或你自己特定于解决方案的代码。 若要了解有关 IoT Edge 模块的详细信息，请参阅[了解 Azure IoT Edge 模块](../iot-edge/iot-edge-modules.md)。

**容器套餐类型和 IoT Edge 模块套餐类型之间的区别是什么？**

IoT Edge 模块套餐类型是在 IoT Edge 设备上运行的一种特定类型的容器。 它附带默认的配置设置以便在 IoT Edge 上下文中运行，并根据需要使用 IoT Edge 模块 SDK 来与 IoT Edge 运行时集成。

## <a name="publishing-your-iot-edge-module"></a>发布 IoT Edge 模块

**选择正确的在线商店**

IoT Edge 模块仅发布到 Azure Marketplace;AppSource 不适用。 有关在线商店之间的差异的详细信息，请参阅 [确定发布选项](determine-your-listing-type.md)。

**计费选项**

市场当前支持 IoT Edge 模块的“免费”和“自带许可证 (BYOL)”计费选项。

### <a name="publishing-options"></a>发布选项

在所有情况下，IoT Edge 模块应选择“Transact”发布选项。  有关发布选项的详细信息，请参阅[选择发布选项](determine-your-listing-type.md)。  

## <a name="eligibility-criteria"></a>资格条件

Microsoft Azure 市场协议和策略的所有条款都适用于 IoT Edge 模块套餐。  此外，还有 IoT Edge 模块的先决条件和技术要求。  

### <a name="prerequisites"></a>必备条件

若要将 IoT Edge 模块发布到 Azure 市场，需满足以下先决条件：

- 访问合作伙伴中心。 有关详细信息，请参阅 [在合作伙伴中心创建商业 marketplace 帐户](partner-center-portal/create-account.md)。
- 在 Azure 容器注册表中托管 IoT Edge 模块。
- 准备好 IoT Edge 模块元数据，比如（非详尽列表）：
    - 标题
    - 说明（采用 HTML 格式）
    - 徽标图像 (大小为 48 x 48 (可选) 、90 x 90 (可选) ，以及从 216 x 216 到 350 x 350 px，均为 PNG 格式) 
    - 使用条款和隐私策略
    - 默认模块配置（路由、孪生所需属性、createOptions、环境变量）
    - 文档
    - 支持联系人

### <a name="technical-requirements"></a>技术要求

若要详细了解帮助 IoT Edge 模块获得认证并在 Azure 市场中发布的主要技术要求，请参阅[准备 IoT Edge 模块技术资产](./partner-center-portal/create-iot-edge-module-asset.md)。

## <a name="next-steps"></a>后续步骤

- 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
- 在合作伙伴中心[创建 IoT Edge 模块产品/服务](./partner-center-portal/azure-iot-edge-module-creation.md)。
