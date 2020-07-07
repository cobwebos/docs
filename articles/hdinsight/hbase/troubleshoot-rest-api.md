---
title: 用于在 Azure HDInsight 中查询 Apache HBase 的 REST API
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 3bf63aa08ec4c1deff2551cfcc0cf188a75261bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82515477"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>用于在 Azure HDInsight 中查询 Apache HBase 的 REST API

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

使用 Apache HBase REST 接口查询非默认命名空间下的表会导致运行时错误（HTTP 状态 500）。

## <a name="cause"></a>原因

HBase REST API 仅在使用默认命名空间时才受支持。 对于使用 HBase 命名空间或使用 HDInsight 上的 REST 服务器对具有列族的列进行调用以引用特定 GET，这是一个已知问题。 这是因为 HDInsight 网关存在安全问题。 使用 API 创建具有命名空间的表，通过列族访问列时，需要指定 `:` 字符，这在 IIS 网关模块中被认为是一个安全问题。

## <a name="mitigation"></a>缓解措施

通过在与 HDInsight 群集位于同一 Azure VNet 中的 VM 上部署应用程序，绕过网关/REST 服务器。 然后，可以直接通过 RPC 与 HBase 通信（完全绕过 REST 服务器），也可以绕过 HDInsight 网关，与运行在工作器节点上的各个 REST 服务器通信。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
