---
title: 在 Azure HDInsight 中查询 Apache HBase REST API
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 3bf63aa08ec4c1deff2551cfcc0cf188a75261bc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515477"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>在 Azure HDInsight 中查询 Apache HBase REST API

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

使用 Apache HBase REST 接口查询非默认命名空间下的表会导致运行时错误（HTTP 状态为500）。

## <a name="cause"></a>原因

仅当使用默认命名空间时才支持 HBase REST API。 这是有关使用 HBase 命名空间的已知问题，或对使用 HDInsight 上的 REST 服务器的列系列引用特定的调用。 这是因为 HDInsight 网关存在安全问题。 使用 API 创建具有命名空间的表时，通过列系列访问列时，需要指定`:`字符，这在 IIS 网关模块中被视为安全问题。

## <a name="mitigation"></a>缓解

通过在与 HDInsight 群集位于同一 Azure VNet 中的 VM 上部署应用程序，绕过网关/REST 服务器。 然后，你可以直接通过 RPC （完全绕过 REST 服务器）或运行在工作节点上的单独 REST 服务器与 HBase 网关进行通信。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
