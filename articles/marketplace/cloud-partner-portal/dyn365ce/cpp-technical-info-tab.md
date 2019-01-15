---
title: Dynamics 365 for Customer Engagement 技术信息选项卡 - Azure 市场 | Microsoft Docs
description: 如何为 AppSource 市场中的 Dynamics 365 for Customer Engagement 应用程序指定技术信息。
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: pbutlerm
ms.openlocfilehash: 214abd64232130dd3fd5fdde510f7545732ac82e
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081584"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 for Customer Engagement 技术信息选项卡

使用“新建套餐”页上的“技术信息”选项卡可以指定有关 Dynamics 365 for Customer Engagement 应用程序的详细信息，包括 CRM 包和营销徽标资产。  此选项卡划分为四个部分：“应用程序信息”、“CRM 包”、“CRM 包可用性”和“营销项目”。 附有星号 (*) 的字段表示必填字段。 


## <a name="application-info-section"></a>“应用程序信息”部分

在此部分提供有关 Dynamics 365 应用程序的详细信息。

![“技术信息”选项卡的“应用程序信息”部分](./media/dynce-technical-info-tab1.png)

下表描述了这些字段。

|      字段                    |    Description                  |
|    ---------                  |  ---------------                |
|   基本许可模型          |  许可模型确定如何在 Dynamics 365 管理中心将客户分配到应用程序。 “资源”许可模型基于实例，而“用户”许可证是按每个租户分配的。  |
|  S2S 出站和 CRM 安全存储访问 |  用于配置 CRM 安全存储或服务器间 (S2S) 出站访问。 *此功能要求遵循 Dynamics 365 团队在认证阶段专业提出的注意事项。* Microsoft 将与你取得联系，以完成附加的步骤来支持此功能。  |
| 订阅 CRM 生命周期事件 | 与 Dynamics 365 生命周期事件集成需要提供一个专用服务，该服务通过与 Microsoft 达成的特殊协议进行注册。 *此功能要求遵循 Dynamics 365 团队在认证阶段专业提出的注意事项。* Microsoft 将与你取得联系，以完成附加的步骤来支持此功能。  |
| 应用程序配置 URL | 让用户配置应用程序的网页的 URL |
| 适用的 Dynamics 365 产品  | 选择此套餐适用的 Dynamics 365 产品。 此套餐将显示在 AppSource 中选定产品的下面。  |
| 仅限营销的更改         | 将此选项设置为“是”表示对现有套餐做出仅限营销内容/描述内容的更改。  进行此类更改可让套餐绕过认证和预配阶段。  |
|  |  |


## <a name="crm-package-section"></a>“CRM 包”部分

在此部分提供有关 AppSource 包文件的详细信息。  Dynamics 365 验证和认证团队将使用此信息。

![“技术信息”选项卡的“CRM 包”部分](./media/dynce-technical-info-tab2.png)

下表描述了这些字段。

|      字段                    |    Description                  |
|    ---------                  |  ---------------                |
|  包的文件名     |  包的文件名 (.zip)。  此名称不是公开的，由 Dynamics 365 认证团队在内部使用。  |
|  URL                          |  包含已上传包文件的 Azure 存储帐户的 URL。 此 URL 应该包含只读的 SAS 密钥，使我们的团队能够选取要验证的包。  |
| 多个 CRM 包     | 仅当支持多个版本的、包含不同包的 CRM 时，才选择“是”。  每个版本包含必须单独创建的相应包文件。  |
| 方案和用例资产   | 用于上传应用程序的功能规范文档，供 Dynamics 365 验证团队使用。  此规范的首选格式是 [E2E 用户方案模板](http://download.microsoft.com/download/5/1/8/51812AC9-BCD8-489F-937C-5D439C494EC1/E2E%20User%20Scenario%20Template.docx)。  |
|  |  |


## <a name="crm-package-availability-section"></a>“CRM 包可用性”部分

在此部分选择要在哪些地理区域向客户推出该应用程序。  部署到以下主权区域需要特殊的权限，并在认证过程中进行验证：[德国](https://docs.microsoft.com/azure/germany/)、[美国政府云](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)和 TIP。


## <a name="marketing-artifacts-section"></a>“营销项目”部分

在此部分需要上传应用程序徽标，用于在 AppSource 市场中表示你的包。  徽标图像必须采用 PNG 格式，大小为 255 x 115 像素。


## <a name="next-steps"></a>后续步骤

我们建议参考[体验版选项卡](./cpp-testdrive-tab.md)一文提供应用程序的演示
