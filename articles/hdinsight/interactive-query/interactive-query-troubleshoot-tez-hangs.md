---
title: Apache Tez 应用程序在 Azure HDInsight 中挂起
description: Apache Tez 应用程序在 Azure HDInsight 中挂起
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: ec5a0d6e8c0a5236ae3929560e81033d983d4dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895112"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>方案：Apache Tez 应用程序悬挂在 Azure HDInsight 中

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

提交 Apache Hive 作业后，从 Tez 视图中作业状态为"正在运行"，但似乎未取得任何进展

## <a name="cause"></a>原因

提交的作业太多；长 Yarn 队列。

## <a name="resolution"></a>解决方法

纵向扩展群集，或者只是等到 Yarn 队列排空。

默认情况下，`yarn.scheduler.capacity.maximum-applications` 控制正在运行或挂起的应用程序的最大数量，默认为 `10000`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 用于改善客户体验的官方 Microsoft Azure 帐户连接。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
