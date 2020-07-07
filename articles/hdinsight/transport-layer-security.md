---
title: Azure HDInsight 中的传输层安全性
description: 传输层安全性 (TLS) 和安全套接字层 (SSL) 是提供计算机网络通信安全的加密协议。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183494"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight 中的传输层安全性

通过公共群集终结点 `https://CLUSTERNAME.azurehdinsight.net` 连接到 HDInsight 群集的操作通过群集网关节点代理。 这些连接使用称为 TLS 的协议进行保护。 在网关上强制执行较高版本的 TLS 可提高这些连接的安全性。 若要详细了解为何应使用较新版本的 TLS，请参阅[解决 TLS 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)。

默认情况下，Azure HDInsight 群集会接受公共 HTTPS 终结点上的 TLS 1.2 连接，以及可以后向兼容的较旧版本。 在群集创建过程中，可以使用 Azure 门户或资源管理器模板来控制网关节点支持的最低 TLS 版本。 对于门户，请在群集创建过程中从“安全性 + 网络”选项卡中选择 TLS 版本。**** 对于部署时的资源管理器模板，请使用 minSupportedTlsVersion**** 属性。 有关示例模板，请参阅 [HDInsight 最低 TLS 1.2 快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 此属性支持三个值：“1.0”、“1.1”和“1.2”，分别对应于 TLS 1.0+、TLS 1.1+ 和 TLS 1.2+。

> [!IMPORTANT]
> 从 2020 年 6 月 30 日开始，Azure HDInsight 将为所有 HTTPS 连接强制实行 TLS 2.1 或更高版本。 我们建议你确保所有客户端都已准备好处理 TLS 1.2 或更高版本。 有关详细信息，请参阅 [Azure HDInsight TLS 1.2 强制](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)。

## <a name="next-steps"></a>后续步骤

* [规划 Azure HDInsight 的虚拟网络](./hdinsight-plan-virtual-network-deployment.md)
* [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。
* [网络安全组](../virtual-network/security-overview.md)。
