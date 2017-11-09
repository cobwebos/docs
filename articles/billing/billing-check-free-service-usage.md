---
title: "监视和跟踪免费服务的使用情况 - Azure | Microsoft Docs"
description: "学习如何检查免费服务的使用情况。 使用 Azure 门户和 csv 使用文件。"
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 27ff6c92904a0b32cd4a37c8b1930adc121a7231
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>检查 Azure 免费帐户中包含的免费服务的使用情况 

我们不会对 Azure 免费帐户中包含的免费服务收费，除非超出这些服务的限值。 为了保持在限值内，可使用 Azure 门户或使用文件来监视及跟踪免费服务的使用情况。 

## <a name="check-usage-on-the-azure-portal"></a>在 Azure 门户中检查使用情况

1.  登录到 [Azure 门户]( http://portal.azure.com)。

2.  在左侧导航窗格底部选择“更多服务”。

3.  选择“订阅”。

4.  选择注册免费帐户时创建的订阅。

    ![显示所有订阅的屏幕截图](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  概述部分显示有关订阅的基本信息，比如订阅 ID、提供类型和订阅名称。 还可找到有关免费帐户信用额度何时过期的信息。

    ![显示订阅基本信息的屏幕截图](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  向下滚动查看当前和预计成本的信息。 成本包括使用免费帐户中未包含服务的费用及超出免费服务限值的使用量的费用。 

    ![显示订阅成本信息的屏幕截图](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  概述部分的最后部分提供有关免费服务使用情况的表格。 

    ![显示免费服务使用情况的屏幕截图](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    该表包含以下列：

* **测定仪名称：**确定要使用的测定仪的度量单位。 若要了解有关服务测定仪映射的信息，请参阅[了解免费服务测定仪映射](billing-understand-free-service-meter-mapping.md)。 
* **使用情况/限值：**当前月份的测定仪使用情况和限值。 还可在状态栏中找到此信息。
* **状态：**测定仪的使用状态。 根据使用模式，可能会具有以下其中一种状态。
  * **未使用：**未使用测定仪或测定仪的使用情况尚未提交至计费系统。
  * **于 \<Date> 超过：**已于 \<Date> 超过测定仪限值。
  * **不可能超过：**不太可能超过测定仪的限值。
  * **于 \<Date> 超过：**可能会于 \<Date> 超过测定仪限值。


## <a name="check-usage-through-the-usage-file"></a>通过使用文件检查使用情况

使用文件提供有关 Azure 订阅的详尽信息。 可从 Azure 帐户中心下载每月和每日使用文件。 若要了解如何下载使用文件并了解所需的访问权限，请参阅[获取发票和使用情况](billing-download-azure-invoice-daily-usage-date.md)。 若要了解有关使用文件中列的信息，请参阅[了解有关使用情况的术语](billing-understand-your-usage.md)。 

使用文件包含免费服务和付费服务的使用情况信息。 免费服务测定仪在测定仪名称末尾附加 **Free**。 若要查找免费测定仪，请在 Excel 中打开文件并筛选“测定仪类别列”，获取包含文本“**- Free**”的单元格（使用文本筛选器 &rarr; 包含筛选器）&nbsp;

![显示免费服务使用情况的屏幕截图](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
