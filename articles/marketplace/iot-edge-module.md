---
title: Azure IoT Edge 模块
description: Azure 市场中的 IoT Edge 模块套餐适用于应用和服务发布者。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/22/2018
ms.author: qianw211
ms.openlocfilehash: ecc892a38d5e86a089085cd67a78ce7d00c86fd8
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181110"
---
# <a name="iot-edge-modules"></a>IoT Edge 模块

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 平台由 Azure 云提供支持。  用户可以通过此平台部署云工作负载以直接在 IoT 设备上运行。  IoT Edge 模块可以运行脱机工作负载，并在本地执行数据分析。 此套餐类型可帮助节省带宽、保护本地数据和敏感数据，并提供低延迟响应时间。  现在，你可以选择利用这些预生成的工作负载。 到目前为止，只有少量来自 Microsoft 的第一方解决方案可用。  必须投入时间和资源来构建你自己的自定义 IoT 解决方案。

通过引入 [Azure 市场中的 IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)，我们现在有单个目标，让发布者向 IoT 受众公开和销售其解决方案。 最终，IoT 开发人员可以查找和购买功能，以便加速其解决方案的开发进程。  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure 市场中 IoT Edge 模块的主要优点：

| **对于发布者**    | **对于客户（IoT 开发人员）**  |
| :------------------- | :-------------------|
| 让数百万开发人员着眼于构建和部署 IoT Edge 解决方案。  | 使用经测试的安全组件，满怀信心地构建 IoT Edge 解决方案。 |
| 发布一次并在任何支持容器的 IoT Edge 硬件上运行。 | 通过查找和部署第一方和第三方 IoT Edge 模块来缩短上市时间，以满足特定需求。 |
| 通过灵活的计费选项进行盈利 <ul> <li> 免费和自带许可证 (BYOL)。 </li> </ul> | 使用所选的计费模式购买。 <ul> <li> 免费和自带许可证 (BYOL)。 </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge 模块是什么？

通过 Azure IoT Edge，可让你以模块的形式来部署和管理 Edge 上的业务逻辑。 Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Microsoft 服务（如 Azure 流分析）、第三方服务，或你自己特定于解决方案的代码。 若要了解有关 IoT Edge 模块的详细信息，请参阅[了解 Azure IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。

**容器套餐类型和 IoT Edge 模块套餐类型之间的区别是什么？**

IoT Edge 模块套餐类型是在 IoT Edge 设备上运行的一种特定类型的容器。 它附带默认的配置设置以便在 IoT Edge 上下文中运行，并根据需要使用 IoT Edge 模块 SDK 来与 IoT Edge 运行时集成。

## <a name="publishing-your-iot-edge-module"></a>发布 IoT Edge 模块

**选择正确的店面**

IoT Edge 模块仅发布到 Azure 市场，AppSource 不适用。  有关店面差异和目标受众的详细信息，请参阅[确定解决方案的发布选项](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)。
 
**计费选项**

市场当前支持 IoT Edge 模块的“免费”和“自带许可证 (BYOL)”计费选项。
 
**发布选项**

在所有情况下，IoT Edge 模块应选择“Transact”发布选项。  有关发布选项的详细信息，请参阅[选择发布选项](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)。  

## <a name="eligibility-criteria"></a>资格条件

Microsoft Azure 市场协议和策略的所有条款都适用于 IoT Edge 模块套餐。  此外，还有 IoT Edge 模块的先决条件和技术要求。  

**先决条件**

若要将 IoT Edge 模块发布到 Azure 市场，需满足以下先决条件：

- 访问云合作伙伴门户 (CPP)。 有关详细信息，请参阅 [Azure 市场和 AppSource 发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。
- 在 Azure 容器注册表中托管 IoT Edge 模块。 
- 准备好 IoT Edge 模块元数据，比如（非详尽列表）： 
    - 标题
    - 说明（采用 HTML 格式）
    - 徽标图像（PNG 格式和包括 40x40px、90x90px、115x115px、255x115px 的固定图像大小）
    - 使用条款和隐私策略
    - 默认模块配置（路由、孪生所需属性、createOptions、环境变量）
    - 文档
    - 支持联系人

**技术要求**

若要详细了解帮助 IoT Edge 模块获得认证并在 Azure 市场中发布的主要技术要求，请参阅[云发布门户](https://cloudpartner.azure.com/)上的 [IoT Edge 模块认证过程](https://cloudpartner.azure.com/#documentation/iot-edge-module-certification-process)。  

## <a name="documentation-and-resources"></a>文档和资源

在登录到[云发布门户](https://cloudpartner.azure.com/)时，可使用以下文章：

- [创建 IoT Edge 模块套餐](https://cloudpartner.azure.com/#documentation/create-iot-edge-module-offer) -– 在云发布门户中发布新 IoT Edge 模块套餐的步骤。
- [IoT Edge 模块认证过程](https://cloudpartner.azure.com/#documentation/iot-edge-module-certification-process) - 认证 IoT Edge 模块的步骤和要求概述。
- [IoT Edge 模块常见问题解答](https://cloudpartner.azure.com/#documentation/iot-edge-module-faq) -– 与 IoT Edge 模块相关的常见问题列表。

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，

- 请[在 Microsoft 合作伙伴网络中注册](https://partner.microsoft.com/membership)。
- 创建 [Microsoft 帐户](https://account.microsoft.com/account/)（Azure 市场交易套餐的必需步骤；其他的建议步骤）。
- 提交[市场注册表](https://azuremarketplace.microsoft.com/sell/signup)。

如果已注册并正在创建新套餐或正在使用现有套餐，

- 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)创建或完成套餐。
