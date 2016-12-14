---
title: "了解 Azure 应用商店基于使用量的报表和卖方 Insights 报表 | Microsoft Docs"
description: "作为 Azure 应用商店上的卖方，了解基于使用量的报表，也称为卖方 Insights 报表"
services: Azure Marketplace
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: v-jeana; hascipio; v-dabosl
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51452ce98a02f3bcea9914b741c611b00f862cc6


---
# <a name="understand-your-seller-insights-report"></a>了解卖方 Insights 报表
**什么是卖方 Insights？**

所有 VM 和基于使用量的计费开发人员服务发布者每个月都会收到来自 Microsoft 的报表（如果他们的产品/服务产生使用量）。

**我将收到什么？**

* **欢迎电子邮件：**作为新的发布者，你将收到一封欢迎电子邮件，通知你将开始收到卖方 Insights 每月报表。
* **每月销售报表：**如果有使用量，你将收到第二封电子邮件，其中包含报表以及有关如何访问密码的说明：
  
  * 如果所拥有的 VM 或基于使用量的计费开发人员服务 SKU 产生使用量，每月报表将显示有关产品/服务的订单、使用量、市场和客户细节的详细信息。
  * 为了保护客户数据，报表使用密码进行锁定，该密码只有你和 Microsoft 知道。
  * 如果产品/服务当月未产生使用量，Microsoft 将不会发送报表。

## <a name="understand-your-seller-insights-report"></a>了解卖方 Insights 报表
**按 SKU 和许可证类型分类的订单：应用商店订单选项卡**

![readingreportbyorders][2]

* 切片器可帮助你按每个元素对报表进行筛选。
* 图表显示了按 Azure 许可证类型分类的每月订单。 每一栏显示按 Azure 许可证类型分类的该月订单总计。
* 图表显示按 SKU 分类的每月订单。 每一栏显示按 SKU 分类的所有 SKU 的每月订单总计。
* 图表显示按 Azure 许可证类型和 Azure 应用商店许可证类型分类的每月订单趋势。
* 饼图显示按 Azure 许可证类型和应用商店许可证类型分类的订单。
* 表显示按应用商店许可证类型、每月总计和所有月份累计总计分类的每月订单总计。

**按 SKU 和许可证类型分类的订单：应用商店使用量选项卡**

![readingreportbyusage][3]

* 切片器可帮助你按每个元素对报表进行筛选。
* 应选择规范化的 VM 使用量或原始使用量。
* 图表显示了按 Azure 许可证类型分类的每月使用量。 每一栏显示按 Azure 许可证类型分类的该月使用量总计。
* 图表显示按 SKU 分类的每月使用量。 每一栏显示按 SKU 分类的所有 SKU 的每月使用量总计。
* 图表显示按 Azure 许可证类型和应用商店许可证类型分类的每月使用量趋势。
* 饼图显示按 Azure 许可证类型和应用商店许可证类型分类的使用量。
* 表显示按应用商店许可证类型、每月总计和所有月份累计总计分类的每月使用量总计。

**订单数据和使用量数据选项卡**

这些选项卡提供用于生成报表的详细数据。

![orderdata][4]

![usagedata][5]

**按 SKU 和许可证类型分类的使用量：客户选项卡**

![customerstab][6]

* 请注意保密条款。
* 此选项卡包含按 SKU、配置文件信息、交易日期和选择推广联系人分类的客户列表。
* 报表包含按 SKU 的订单计数和总计。

**法律免责声明**

![法律][1]

请仔细阅读法律免责声明。 如果你有任何问题或反馈，请单击免责声明底部的链接，路由到应用商店支持页。

## <a name="request-a-password-reminder"></a>请求密码提示
导航到 https://publish.windowsazure.com/ 并使用 Microsoft 帐户凭据登录。
![passwordreminder][7]

选择“发布者”选项卡。
![selectpublisherstab][8]

在 URL 中找到发布者 ID：

* 将此 ID 用作密码以打开卖方 Insights Excel 文件。
  在进一步通知之前，这就是你的密码。
* 我们建议将 Microsoft Office 2013 与 Windows 结合使用来作为选择的工作簿读取器。  某些用户报告在使用 Microsoft Office for Mac 时存在问题。

![publisherid][9]

## <a name="next-steps"></a>后续步骤
如果你对报表和 Insights 有任何问题，请与我们的支持团队联系：

1. 导航到 https://publish.windowsazure.com/ 处的支持页。
2. 在“问题类型”框中，选择“报表和 Insights”。
3. 在“类别”框中，选择“与报表相关的问题”。
4. 单击“启动请求”。
   ![sellerinsightsquestions][10]

[1]: ./media/marketplace-publishing-report-seller-insights/legal.png
[2]: ./media/marketplace-publishing-report-seller-insights/readingreportbyorders.png
[3]: ./media/marketplace-publishing-report-seller-insights/readingreportbyusage.png
[4]: ./media/marketplace-publishing-report-seller-insights/orderdata.png
[5]: ./media/marketplace-publishing-report-seller-insights/usagedata.png
[6]: ./media/marketplace-publishing-report-seller-insights/customerstab.png
[7]: ./media/marketplace-publishing-report-seller-insights/passwordreminder.png
[8]: ./media/marketplace-publishing-report-seller-insights/selectpublisherstab.png
[9]: ./media/marketplace-publishing-report-seller-insights/publisherid.png
[10]: ./media/marketplace-publishing-report-seller-insights/sellerinsightsquestions.png



<!--HONumber=Nov16_HO3-->


