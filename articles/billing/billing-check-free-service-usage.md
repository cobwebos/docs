---
title: 监视和跟踪 Azure 免费服务使用情况
description: 了解如何在 Azure 门户和使用情况 CSV 文件中检查免费服务使用情况。
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ae8b26f0032c6659da18b822f4f8568995eff82b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709763"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>检查 Azure 免费帐户中包含的免费服务的使用情况

我们不会对 Azure 免费帐户中包含的免费服务收费，除非超出这些服务的限值。 为了保持在限值内，可使用 Azure 门户或使用情况文件来监视及跟踪免费服务使用情况。

## <a name="check-usage-in-the-azure-portal"></a>在 Azure 门户中检查使用情况

1.  登录到 [Azure 门户](https://portal.azure.com)。

2.  在左侧的导航区域中，选择“所有服务”  。

3.  选择 **订阅**。

4.  选择注册免费帐户时创建的订阅。

    ![显示所有订阅的屏幕截图](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  “概述”部分显示有关订阅的基本信息。 例如，订阅 ID、套餐类型和订阅名称。 还可找到有关免费帐户额度何时到期的信息。

    ![显示订阅基本信息的屏幕截图](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  向下滚动以查找有关当前成本和预测成本的信息。 成本包括免费帐户中未包含的服务使用费用以及超出免费服务限额的使用费用。

    ![显示订阅成本信息的屏幕截图](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  “概述”部分的最后部分提供了一个表格，其中显示了免费服务的使用情况。

    ![显示免费服务使用情况的屏幕截图](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    该表具有以下列：

* **测定仪名称：** 确定要使用的测定仪的度量单位。 若要了解有关服务测定仪映射的信息，请参阅[了解免费服务测定仪映射](billing-understand-free-service-meter-mapping.md)。
* **使用情况/限值：** 当前月份的测定仪使用情况和限值。 还可在状态栏中找到此信息。
* **状态：** 测定仪的使用状态。 根据使用模式，可能会具有以下状态之一：
  * **未使用：** 未使用测定仪或测定仪的使用情况尚未提交至计费系统。
  * **于 \<Date> 超过：** 已于 \<Date> 超过测定仪限值。
  * **不可能超过：** 不太可能超过测定仪的限值。
  * **于 \<Date> 超过：** 可能会于 \<Date> 超过测定仪限值。

## <a name="check-usage-with-the-usage-file"></a>通过使用情况文件检查使用情况

使用情况文件提供有关 Azure 订阅的详细信息。 可从 Azure 帐户中心下载每月和每日使用情况文件。 若要了解如何下载使用文件并了解所需的访问权限，请参阅[获取发票和使用情况](billing-download-azure-invoice-daily-usage-date.md)。 若要了解有关使用文件中列的信息，请参阅[了解有关使用情况的术语](billing-understand-your-usage.md)。

使用文件具有免费服务和付费服务的使用情况信息。 免费服务测定仪在测定仪名称末尾附加 **Free**。 若要查找免费测定仪，请在 Excel 中打开文件并筛选“测定仪类别”列，获取具有文本“- Free”的单元格（使用文本筛选器 &rarr; 包含筛选器）   。


![显示免费服务使用情况的屏幕截图](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [升级订阅](billing-upgrade-azure-subscription.md)
