---
title: 将 Azure Kubernetes 服务与 Kafka on HDInsight 配合使用 | Microsoft Docs
description: 了解如何使用 Azure Kubernetes 服务 (AKS) 托管的容器映像中的 Kafka on HDInsight。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: f54039a0e702aa3c789363969120e000760f6ef5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885955"
---
# <a name="use-azure-kubernetes-service-with-kafka-on-hdinsight"></a>将 Azure Kubernetes 服务与 Kafka on HDInsight 配合使用

了解如何将 Azure Kubernetes 服务 (AKS) 与 Kafka on HDInsight 群集配合使用。 本文档中的步骤使用 AKS 中托管的 Node.js 应用程序来验证与 Kafka 的连接。 此应用程序使用 [kafka-node](https://www.npmjs.com/package/kafka-node) 包来与 Kafka 通信。 它使用 [Socket.io](https://socket.io/) 在浏览器客户端与托管在 AKS 中的后端之间进行事件驱动的消息传送。

[Apache Kafka](https://kafka.apache.org) 是一个分布式流式处理平台，以开源方式提供，可用于构建实时流式处理数据管道和应用程序。 Azure Kubernetes 服务管理托管的 Kubernetes 环境，使用它可以快速轻松地部署容器化应用程序。 使用 Azure 虚拟网络可以连接这两个服务。

> [!NOTE]
> 本文重点讲解需要执行哪些步骤才能让 Azure Kubernetes 服务与 Kafka on HDInsight 通信。 示例本身只是一个用于演示配置工作原理的基本 Kafka 客户端。

## <a name="prerequisites"></a>先决条件

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Azure 订阅

本文档假设读者熟悉如何创建和使用以下 Azure 服务：

* Kafka on HDInsight
* Azure Kubernetes 服务
* Azure 虚拟网络

此外，本文档还假设读者已学完了 [Azure Kubernetes 服务教程](../../aks/tutorial-kubernetes-prepare-app.md)。 本教程将创建一个容器服务、Kubernetes 群集和容器注册表，并配置 `kubectl` 实用工具。

## <a name="architecture"></a>体系结构

### <a name="network-topology"></a>网络拓扑

HDInsight 和 AKS 使用 Azure 虚拟网络作为计算资源的容器。 若要在 HDInsight 与 AKS 之间实现通信，必须在两者的网络之间启用通信。 本文档中的步骤对这些网络使用虚拟网络对等互连。 其他连接（例如 VPN）应该也能正常工作。 有关对等互连的详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)文档。


下图演示了本文档中使用的网络拓扑：

![HDInsight 和 AKS 各在不同的虚拟网络中，两者的网络使用对等互连建立连接](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> 未在对等网络之间启用名称解析，因此使用了 IP 寻址。 默认情况下，Kafka on HDInsight 配置为在客户端建立连接时返回主机名而不是 IP 地址。 本文档中的步骤会将 Kafka 修改为使用 IP 播发。

## <a name="create-an-azure-kubernetes-service-aks"></a>创建 Azure Kubernetes 服务 (AKS)

如果还没有 AKS 群集，可以参考以下文档之一了解如何创建 AKS 群集：

* [部署 Azure Kubernetes 服务 (AKS) 群集 - 门户](../../aks/kubernetes-walkthrough-portal.md)
* [部署 Azure Kubernetes 服务 (AKS) 群集 - CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> AKS 在安装期间会创建虚拟网络。 此网络将与下一部分针对 HDInsight 创建的网络建立对等互连。

## <a name="configure-virtual-network-peering"></a>配置虚拟网络对等互连

1. 在 [Azure 门户](https://portal.azure.com)中选择“资源组”，找到包含 AKS 群集虚拟网络的资源组。 该资源组名为 `MC_<resourcegroup>_<akscluster>_<location>`。 `resourcegroup` 和 `akscluster` 条目是在其中创建群集的资源组的名称，并且是该群集的名称。 `location` 是在其中创建群集的位置。

2. 在该资源组中，选择“虚拟网络”资源。

3. 选择“地址空间”。 记下列出的地址空间。

4. 若要为 HDInsight 创建虚拟网络，请依次选择“+ 创建资源”、“网络”、“虚拟网络”。

    > [!IMPORTANT]
    > 输入新虚拟网络的值时，使用的地址空间不得与 AKS 群集网络使用的地址空间重叠。

    使用 AKS 群集所用的相同虚拟网络__位置__。

    等到创建完虚拟网络，然后转到下一步。

5. 若要在 HDInsight 网络与 AKS 群集网络之间配置对等互连，请选择虚拟网络，然后选择“对等互连”。 选择“+ 添加”，然后使用以下值填充表单：

    * __名称__：输入此对等互连配置的唯一名称。
    * __虚拟网络__：使用此字段选择 **AKS 群集**的虚拟网络。

    将其他所有字段保留默认值，然后选择“确定”以配置对等互连。

6. 若要在 AKS 群集网络与 HDInsight 网络之间配置对等互连，请选择“AKS 群集虚拟网络”，然后选择“对等互连”。 选择“+ 添加”，然后使用以下值填充表单：

    * __名称__：输入此对等互连配置的唯一名称。
    * __虚拟网络__：使用此字段选择 __HDInsight 群集__的虚拟网络。

    将其他所有字段保留默认值，然后选择“确定”以配置对等互连。

## <a name="install-kafka-on-hdinsight"></a>安装 Kafka on HDInsight

创建 Kafka on HDInsight 群集时，必须加入前面针对 HDInsight 创建的虚拟网络。 有关创建 Kafka 群集的详细信息，请参阅[创建 Kafka 群集](apache-kafka-get-started.md)文档。

> [!IMPORTANT]
> 创建群集时，必须使用“高级设置”加入针对 HDInsight 创建的虚拟网络。

## <a name="configure-kafka-ip-advertising"></a>配置 Kafka IP 播发

使用以下步骤将 Kafka 配置为播发 IP 地址而不是域名：

1. 使用 Web 浏览器转到 https://CLUSTERNAME.azurehdinsight.net。 将 CLUSTERNAME 替换为 Kafka on HDInsight 群集的名称。

    出现提示时，使用群集的 HTTPS 用户名称密码。 将显示群集的 Ambari Web UI。

2. 要查看 Kafka 的相关信息，请从左侧列表中选择“Kafka”。

    ![Kafka 突出显示的服务列表](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. 要查看 Kafka 配置，请在顶端的中间位置选择“配置”。

    ![Kafka 的配置链接](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. 要查找“kafka-env” 配置，请在右上方的“筛选器”字段中输入 `kafka-env`。

    ![Kafka 配置，适用于 kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. 要配置 Kafka 来播发 IP 地址，请将下列文本添加到“kafka-env-template”字段的底部：

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. 要配置 Kafka 侦听的接口，请在右上方的“筛选器”字段中输入 `listeners`。

7. 要将 Kafka 配置为侦听所有网络接口，请将“侦听器”字段的值更改为 `PLAINTEXT://0.0.0.0:9092`。

8. 单击“保存”按钮保存配置。 输入描述更改的文本消息。 保存更改后，请选择“确定”。

    ![保存配置按钮](./media/apache-kafka-azure-container-services/save-button.png)

9. 要防止在重启 Kafka 时出错，请使用“服务操作”按钮，并选择“打开维护模式”。 选择“确定”完成操作。

    ![服务操作，其中已突出显示“打开维护”](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. 要重启 Kafka，请使用“重启”按钮，并选择“重启所有受影响的项”。 确认重启，在操作完成后再使用“确定”按钮。

    ![重启按钮，其中突出显示了所有受影响的重启项](./media/apache-kafka-azure-container-services/restart-button.png)

11. 要禁用维护模式，请使用“服务操作”按钮，并选择“关闭维护模式”。 选择“确定”完成操作。

## <a name="test-the-configuration"></a>测试配置

此时，Kafka 和 Azure Kubernetes 服务通过对等互连的虚拟网络进行通信。 若要测试此连接，请使用以下步骤：

1. 创建测试应用程序使用的 Kafka 主题。 有关创建 Kafka 主题的信息，请参阅[创建 Kafka 群集](apache-kafka-get-started.md)文档。

2. 从 [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) 下载示例应用程序。

3. 编辑 `index.js` 文件并更改以下行：

    * `var topic = 'mytopic'`：将 `mytopic` 替换为此应用程序使用的 Kafka 主题名称。
    * `var brokerHost = '176.16.0.13:9092`：将 `176.16.0.13` 替换为群集的某个代理主机的内部 IP 地址。

        若要查找群集中代理主机（工作节点）的内部 IP 地址，请参阅 [Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) 文档。 选择域名以 `wn` 开头的某个条目的 IP 地址。

4. 在 `src` 目录中，通过命令行安装依赖项并使用 Docker 生成用于部署的映像：

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > 此应用程序所需的包将签入存储库，因此不需要使用 `npm` 实用工具来安装这些包。

5. 登录到 Azure 容器注册表 (ACR)，并找到 loginServer 名称：

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > 如果不知道 Azure 容器注册表名称，或者不熟悉如何将 Azure CLI 与 Azure Kubernetes 服务配合使用，请参阅 [AKS 教程](../../aks/tutorial-kubernetes-prepare-app.md)。

6. 使用 ACR 的 loginServer 标记本地 `kafka-aks-test` 映像。 另外，在末尾添加 `:v1` 以指示映像版本：

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. 将映像推送到注册表：

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    此操作需要几分钟才能完成。

8. 编辑 Kubernetes 清单文件 (`kafka-aks-test.yaml`)，并将 `microsoft` 替换为在步骤 4 中检索到的 ACR loginServer 名称。

9. 使用以下命令通过清单部署应用程序设置：

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. 使用以下命令监视应用程序的 `EXTERNAL-IP`：

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    分配外部 IP 地址后，使用 __CTRL + C__ 退出监视

11. 打开 Web 浏览器并输入服务的外部 IP 地址。 随后将显示下图所示的页面：

    ![网页图像](./media/apache-kafka-azure-container-services/test-web-page.png)

12. 在字段中输入文本，然后选择“发送”按钮。 数据将发送到 Kafka。 然后，应用程序中的 Kafka 使用者将读取消息，并将其添加到“来自 Kafka 的消息”部分。

    > [!WARNING]
    > 可能会收到消息的多个副本。 如果在连接后刷新浏览器，或者在浏览器中与应用程序建立多个连接，则通常会发生此问题。

## <a name="next-steps"></a>后续步骤

使用以下链接了解如何使用 Apache Kafka on HDInsight：

* [Kafka on HDInsight 入门](apache-kafka-get-started.md)

* [使用 MirrorMaker 创建 Kafka on HDInsight 的副本](apache-kafka-mirroring.md)

* [将 Apache Storm 与 Kafka on HDInsight 结合使用](../hdinsight-apache-storm-with-kafka.md)

* [将 Apache Spark 与 Kafka on HDInsight 结合使用](../hdinsight-apache-spark-with-kafka.md)

* [通过 Azure 虚拟网络连接到 Kafka](apache-kafka-connect-vpn-gateway.md)
