---
title: Azure HDInsight 中的 Apache Tez 应用程序挂起
description: Azure HDInsight 中的 Apache Tez 应用程序挂起
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 9a7b28dd48be7bbd0c789749ee4a1fa01a17d85f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091346"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>场景：Azure HDInsight 中的 Apache Tez 应用程序挂起

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

提交 Apache Hive 作业后，"Tez" 视图中的作业状态为 "正在运行"，但未显示任何进度

## <a name="cause"></a>原因

提交的作业太多;长 Yarn 队列。

## <a name="resolution"></a>分辨率

向上缩放群集，或只需等待 Yarn 队列耗尽。

默认情况`yarn.scheduler.capacity.maximum-applications`下，控制正在运行或挂起的应用程序的最大数目，默认`10000`值为。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
