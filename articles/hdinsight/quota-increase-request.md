---
title: 请求增加 CPU 内核配额 - Azure HDInsight
description: 了解请求增加分配给订阅的 CPU 内核的过程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: a708ad9626f4d6750de64097a7e1e9177c350e5e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83717736"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>请求增加 Azure HDInsight 的配额

CPU 内核配额有助于确保资源使用在特定 Azure 区域的所有客户之间得到公平分配。 但是，在某些情况下，业务要求需要的群集资源量可能会超出当前配额的允许值。 在这种情况下，你可以请求增加 CPU 内核配额，以便能够部署符合你数据处理要求的群集数。

达到配额限制时，则无法部署新群集，也无法通过添加更多工作节点来横向扩展现有群集。 唯一存在配额限制的是每个订阅的区域级别的 CPU 核心配额。 例如，订阅在美国东部地区的 CPU 内核限制可能是 30 个，而在美国东部 2 中允许的内核数又增加了 30 个。

## <a name="gather-required-information"></a>收集所需信息

如果收到了提示已达到配额限制的错误消息，请使用此部分所述的过程，收集重要信息并提交增加配额请求。

1. 确定所需的群集 VM 大小、规模和类型。
1. 查看订阅的当前配额容量限制。 若要查看可用的内核数，请执行以下步骤：

    1. 登录 [Azure 门户](https://portal.azure.com/)。
    1. 导航到 HDInsight 群集的“概述”页面。
    1. 在左侧菜单上，选择“配额限制”。 页面显示正在使用的核心数、可用核心数和总核心数。

若要请求增加配额，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 选择页面左下方的“帮助 + 支持”。

    ![“帮助和支持”按钮](./media/quota-increase-request/help-support-button.png)

1. 选择“新建支持请求”。
1. 在“新建支持请求”页面的“基本信息”选项卡下，选择以下选项 ：

   - “问题类型”：“服务和订阅限制(配额)”
   - “订阅”：想要修改的订阅
   - “配额类型”：**HDInsight**

     ![创建支持请求来增加 HDInsight 核心配额](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. 在完成时选择“下一步:**解决方案 >>”。**
1. 在“详细信息”页面，输入问题的说明，然后选择问题的严重性、首选联系方式和其他必填字段。 请使用下面列出的模板，确保你提供必需的信息。 增加配额请求由 Azure 容量团队进行评估，而非 HDInsight 产品团队。 提供的信息越完整，请求得到批准的可能性就越大。

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![问题详细信息](./media/quota-increase-request/problem-details.png)

1. 在完成时选择“下一步:**查看 + 创建 >>”。**
1. 在“查看 + 创建”选项卡上，选择“创建”。 

> [!NOTE]  
> 如果需要增加专用区域中的 HDInsight 核心配额，请[提交允许列表请求](https://aka.ms/canaryintwhitelist)。

可以[联系支持部门来请求提高配额](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

有一些配额限制是固定的。 例如，单个 Azure 订阅最多可以有 10,000 个内核。 有关这些限制的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何在 HDInsight 中设置和配置群集。
* [监视群集性能](hdinsight-key-scenarios-to-monitor.md)：了解要在 HDInsight 群集中监视的、可能会影响群集容量的关键情况。