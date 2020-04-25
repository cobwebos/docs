---
title: 发布 Azure IoT Edge 模块产品 |Azure Marketplace
description: 如何发布 IoT Edge 模块套餐。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ef73956b1b0d3e7bed6e91cde0b92bcc3e432795
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141845"
---
# <a name="publish-iot-edge-module-offer"></a>发布 IoT Edge 模块套餐

>[!Important]
>从2020年4月13日开始，我们将开始向合作伙伴中心移动 IoT Edge 模块的管理。 迁移后，你将在合作伙伴中心创建和管理你的产品/服务。 按照[创建 IoT Edge 模块提供](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)中的说明来管理迁移的产品/服务。

 通过在“新建套餐”页上提供信息创建新的套餐后，可以发布该套餐。**** 选择“发布”启动发布过程。****

下图显示了发布套餐以使其“上线”的过程的主要步骤。

![IoT Edge 模块套餐的发布步骤](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>发布步骤的详细说明

下表描述了每个发布步骤，以及完成该步骤预计所需的时间（最长时间）。
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **发布步骤**           | **时间**    | **描述**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 验证先决条件         | 15 分钟   | 验证套餐信息和套餐设置。                        |
| 认证                  | 2 周 | Azure 认证团队对套餐进行分析。 此步骤将针对病毒、恶意软件、安全合规性和安全问题执行扫描。 此外，还验证此 IoT Edge 模块套餐是否符合所有资格条件（请参阅[先决条件](./cpp-prerequisites.md)和[准备技术资产](./cpp-create-technical-assets.md)）。 如果发现了问题，则提供反馈。 |
| 打包 | 1 小时  | 产品/服务的技术资产已打包供客户使用，并已配置并设置了线索系统。 |
|  发布者签署             |  -        | 最终发布者在套餐上线之前进行复查和确认。 可以在所选订阅（在套餐信息步骤中）中部署套餐，以验证它是否符合所有要求。  选择“上线”，使套餐可以转移到下一步骤。**** |
| 打包                 | 1 小时 | 在市场生产系统和区域中复制最终的套餐。 | 
| 实时                           | 4 天 |发布套餐，将其复制到所需的区域，并使其可供公众使用。 |

等待最多 10 个工作日，让发布过程完成并发布套餐。 完成发布过程后，你的 IoT Edge 模块套餐将列入 [Microsoft Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。

## <a name="next-steps"></a>后续步骤

- [更新 Azure 市场中的现有 IoT Edge 模块套餐](./cpp-update-existing-offer.md)
