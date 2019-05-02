---
title: Azure SaaS 应用程序产品/服务计划 |Azure Marketplace
description: 在 Azure 市场上为 SaaS 应用程序产品/服务创建计划。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 2ff86b39f67b170ce99b045f5cfa888e06057bbe
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943342"
---
# <a name="saas-application-plans-tab"></a>SaaS 应用程序“计划”选项卡

使用“计划”选项卡创建新计划。 如果要为 SaaS 应用使用“通过 Microsoft 进行销售”选项，则必须添加至少 1 个计划。

![创建新计划](./media/saas-plans-new-plan.png)

## <a name="create-a-new-plan"></a>创建新计划

若要创建新计划，请执行以下操作：

1. 在“计划”下，选择“+ 新建计划”。
2. 在“新建计划”弹出窗口中，键入一个**计划 ID**。 此最大长度为 50 个字符。 此 ID 只能包含小写的字母数字字符、短划线或下划线。 在发布产品/服务后无法更改此 ID。
3. 选择“确定”以保存计划 ID。

   ![新计划的计划 ID](./media/saas-plans-plan-ID.png)

### <a name="to-configure-the-plan"></a>若要配置计划，请执行以下操作：

1. 在“计划详细信息”下，提供以下字段的信息：

   - 标题 - 为此计划提供一个标题。 标题限制为 50 个字符。
   - 说明 - 提供一个说明。 说明限制为最多 500 个字符。
   - 这是否为专用计划？ - 如果此计划仅供一组选定客户使用，请选择“是”。
   - 国家/区域可用性 - 计划必须可供至少 1 个国家或区域使用。 单击“选择区域”。 从“选择国家/区域可用性”列表中选取一个国家/区域，然后选择“确定”。 
   - 旧定价 - 以美元为单位提供每月成本。

2. 在“简化的货币定价”下，提供以下信息：

   - 计费期 - 默认情况下选择每月价格。 还可以提供每年定价。
   - 每月价格 - 提供每月价格，这必须与旧定价匹配。

   >[!NOTE] 
   >如果向计费期添加每年价格，则会提示你以美元为单位提供**每年价格**。

3. 选择“保存”以完成计划配置。

   >[!NOTE] 
   >保存定价更改后，可以导出/导入定价数据。

![配置计划详细信息](./media/saas-plans-plan-details.png)

## <a name="next-steps"></a>后续步骤

[“通道信息”选项卡](./cpp-channel-info-tab.md)

