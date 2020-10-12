---
title: 在 Apache Kafka 中启用自动创建主题功能 - Azure HDInsight
description: 了解如何将 Apache Kafka on HDInsight 配置为自动创建主题。 可以通过将设置为 true 来配置 Kafka，方法是 `auto.create.topics.enable` 通过 Ambari。 或在群集创建过程中通过 PowerShell 或资源管理器模板执行。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 829f91452725615af4d444426e25ffad62d6ab6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087493"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>如何将 Apache Kafka on HDInsight 配置为自动创建主题

默认情况下，Apache Kafka on HDInsight 不允许自动创建主题。 可以使用 Apache Ambari 针对现有群集自动创建主题。 也可以允许使用 Azure 资源管理器模板在创建新的 Kafka 群集时自动创建主题。

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

若要允许通过 Ambari Web UI 在现有的群集上自动创建主题，请执行以下步骤：

1. 从 [Azure 门户](https://portal.azure.com)选择 Kafka 群集。

1. 从**群集仪表板**中，选择“Ambari 主页”****。

    ![群集仪表板处于选中状态的门户的图像](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    出现提示时，进行身份验证使用群集的登录名 (admin) 凭据。 相反，可以直接从 `https://CLUSTERNAME.azurehdinsight.net/` 连接到 Amabri，其中 `CLUSTERNAME` 是 Kafka 群集的名称。

1. 从页面左侧的列表选择 Kafka 服务。

    ![Apache Ambari 服务列表选项卡](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. 在页面中间选择“配置”。

    ![Apache Ambari 服务配置选项卡](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. 在“筛选器”字段中输入值 `auto.create`。

    ![Apache Ambari 搜索筛选器字段](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    此设置筛选属性列表，并显示 `auto.create.topics.enable` 设置。

1. 将 `auto.create.topics.enable` 的值更改为 `true`，然后选择“保存”****。 添加注释，然后选择**保存**。

    ![auto.create.topics.enable 条目的图像](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. 依次选择 Kafka 服务、“重启”、“重启所有受影响的项”。____ ____ 出现提示时，选择“确认全部重启”____。

    !["Apache Ambari 重新启动所有受影响的"](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> 也可通过 Ambari REST API 设置 Ambari 值。 这通常更为困难，因为需进行多次 REST 调用来检索并修改当前配置以及执行其他操作。有关详细信息，请参阅[使用 Apache Ambari REST API 管理 HDInsight 群集](../hdinsight-hadoop-manage-ambari-rest-api.md)文档。

## <a name="resource-manager-templates"></a>Resource Manager 模板

使用 Azure 资源管理器模板创建 Kafka 群集时，可以将 `auto.create.topics.enable` 添加到 `kafka-broker` 中，对其直接进行设置。 以下 JSON 代码片段演示如何将此值设置为 `true`：

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>后续步骤

本文档介绍了如何为 HDInsight 上的 Apache Kafka 启用自动创建主题功能。 若要详细了解如何使用 Kafka，请参阅以下链接：

* [分析 Apache Kafka 日志](apache-kafka-log-analytics-operations-management.md)
* [在 Apache Kafka 集群之间复制数据](apache-kafka-mirroring.md)
