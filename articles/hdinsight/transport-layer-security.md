---
title: Azure HDInsight 中的传输层安全性
description: 传输层安全性（TLS）和安全套接字层（SSL）是通过计算机网络提供通信安全的加密协议。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b74ca75b26d4d98c79091683f428eb39e5827665
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183494"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Azure HDInsight 中的传输层安全性

通过公用群集终结点`https://CLUSTERNAME.azurehdinsight.net`与 HDInsight 群集的连接通过 "群集网关" 节点代理。 这些连接使用称为 TLS 的协议进行保护。 在网关上强制执行较高版本的 TLS 可提高这些连接的安全性。 有关为何应使用较新版本的 TLS 的详细信息，请参阅[解决 tls 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)。

默认情况下，Azure HDInsight 群集接受公有 HTTPS 终结点上的 TLS 1.2 连接和旧版本，以实现向后兼容性。 使用 Azure 门户或资源管理器模板，可以在创建群集期间控制网关节点上支持的最低 TLS 版本。 对于门户，请在群集创建过程中从 "**安全性 + 网络**" 选项卡中选择 TLS 版本。 对于部署时的资源管理器模板，请使用**minSupportedTlsVersion**属性。 有关示例模板，请参阅[HDInsight 最小 TLS 1.2 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 此属性支持三个值： "1.0"、"1.1" 和 "1.2"，分别对应于 TLS 1.0 +、TLS 1.1 + 和 TLS 1.2 +。

> [!IMPORTANT]
> 从2020年6月30日开始，Azure HDInsight 将为所有 HTTPS 连接强制执行 TLS 1.2 或更高版本。 我们建议你确保所有客户端都已准备好处理 TLS 1.2 或更高版本。 有关详细信息，请参阅[Azure HDINSIGHT TLS 1.2 强制](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)。

## <a name="next-steps"></a>后续步骤

* [规划 Azure HDInsight 的虚拟网络](./hdinsight-plan-virtual-network-deployment.md)
* [为 Azure HDInsight 群集创建虚拟网络](hdinsight-create-virtual-network.md)。
* [网络安全组](../virtual-network/security-overview.md)。
