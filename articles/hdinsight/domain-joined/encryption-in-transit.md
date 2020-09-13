---
title: Azure HDInsight 加密传输
description: 了解用于在 Azure HDInsight 群集传输中提供加密的安全功能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 25e38beb561ee954db2987643775f3a3c6e05737
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89668771"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Azure HDInsight 传输中的 IPSec 加密

本文介绍 Azure HDInsight 群集节点之间的通信的传输加密实现。

> [!Note]
> 当前为以下区域启用了传输加密：美国东部、美国中南部、西2。 

## <a name="background"></a>背景

Azure HDInsight 提供了各种安全功能来保护企业数据。 这些解决方案分为外围安全性、身份验证、授权、审核、加密和合规性的支柱。 加密可应用于静态数据和传输中的数据。

静态加密由 Azure 存储帐户上的服务器端加密以及作为 HDInsight 群集一部分的 Azure Vm 上的磁盘加密涵盖。

在 HDInsight 上传输数据时，可通过 [传输层安全性 (TLS) ](https://docs.microsoft.com/azure/hdinsight/transport-layer-security) accssing 群集网关和 [Internet 协议安全性 (](https://en.wikipedia.org/wiki/IPsec) 群集节点之间的 IPSec) 。 可以选择在所有头节点、辅助角色节点、边缘节点和 zookeeper 节点之间启用 IPSec。 不会为在基于 Windows 的 Vm 和群集中的其他基于 linux 的节点的网关或 [id 代理](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker) 节点之间的流量启用此功能。

## <a name="enable-encryption-in-transit"></a>在传输中启用加密

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户启用传输中启用加密的新群集，请执行以下步骤：

1. 开始正常的群集创建过程。 请参阅 [在 HDInsight 中使用初始群集创建步骤创建基于 Linux 的群集 Azure 门户](../hdinsight-hadoop-create-linux-clusters-portal.md) 。
1. 完成 " **基本** 信息" 和 " **存储** " 选项卡。 转到 " **安全 + 网络** " 选项卡。

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="创建群集-安全和网络选项卡。":::

1. 在 " **安全 + 网络** " 选项卡上，单击 " **在传输中启用加密** " 复选框。

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="创建群集-在传输过程中启用加密。":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>在通过 Azure CLI 启用传输中的加密创建群集

使用属性启用传输中的加密 `isEncryptionInTransitEnabled` 。

您可以 [下载示例模板和参数文件](https://github.com/Azure-Samples/hdinsight-enterprise-security)。 在使用下面的模板和 Azure CLI 代码片段之前，请将以下占位符替换为其正确值：

| 占位符 | 说明 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 订阅的 ID |
| `<RESOURCE_GROUP>` | 要在其中创建新群集和存储帐户的资源组。 |
| `<STORAGEACCOUNTNAME>` | 应该与群集一起使用的现有存储帐户。 该名称的格式应为 `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | 你的 HDInsight 群集的名称。 |
| `<PASSWORD>` | 你选择的使用 SSH 及 Ambari 仪表板登录群集的密码。 |
| `<VNET_NAME>` | 将在其中部署群集的虚拟网络。 |

以下代码片段将会执行下述初始步骤：

1. 登录到 Azure 帐户。
1. 设置要在其中执行创建操作的活动订阅。
1. 为新的部署活动创建新的资源组。
1. 部署模板以创建新群集。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 中的企业安全性概述](hdinsight-security-overview.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../disk-encryption.md)。
