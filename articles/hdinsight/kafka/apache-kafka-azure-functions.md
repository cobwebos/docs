---
title: 使用 Azure Functions 将数据发送到 Kafka on HDInsight | Microsoft Docs
description: 了解如何使用 Azure 函数将数据写入 Kafka on HDInsight。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: c657cda7dce0611cb357a0a2063a154f2f6f25f2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>通过 Azure 函数应用使用 Kafka on HDInsight

了解如何创建一个可将数据写入 Kafka on HDInsight 的 Azure 函数应用。

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 是一个无服务器计算服务。 使用它可以按需运行代码，而无需显式预配或管理基础结构。

[Apache Kafka](https://kafka.apache.org) 是一个分布式流式处理平台，以开源方式提供，可用于构建实时流式处理数据管道和应用程序。 Kafka 还提供了类似于消息队列的消息中转站，可在其中发布和订阅命名数据流。 Kafka on HDInsight 可在 Microsoft Azure 云中提供高度可扩展和高度可用的托管服务。

Kafka on HDInsight 不会公共 Internet 上提供 API。 若要发布或使用 Kafka 中的数据，必须使用 Azure 虚拟网络连接到 Kafka 群集。 Azure Functions 提供一种简便方式来创建公共终结点，用于通过虚拟网络网关将数据推送到 Kafka on HDInsight 中。

> [!NOTE]
> 本文重点讲解需要执行哪些步骤才能让 Azure Functions 与 Kafka on HDInsight 通信。 示例本身只是一个用于演示配置工作原理的基本 Kafka 生成器。

## <a name="prerequisites"></a>先决条件

* 一个 Azure 函数应用。 有关详细信息，请参阅[创建第一个函数](../../azure-functions/functions-create-first-azure-function.md)文档。

* 一个 Azure 虚拟网络。 若要使用 Azure Functions，虚拟网络必须使用以下 IP 范围之一：

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    有关详细信息，请参阅[将应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)文档。

* 一个 Kafka on HDInsight 群集。 有关创建 Kafka on HDInsight 群集的信息，请参阅[创建 Kafka 群集](apache-kafka-get-started.md)文档。

    > [!IMPORTANT]
    > 在群集配置期间，必须使用“高级”设置步骤选择 HDInsight 所用的 Azure 虚拟网络和子网。 选择在前面步骤中创建的虚拟网络和子网。

    有关 Kafka 和虚拟网络的详细信息，请参阅[通过虚拟网络连接到 Kafka](apache-kafka-connect-vpn-gateway.md) 文档。

## <a name="architecture"></a>体系结构

Kafka on HDInsight 包含在 Azure 虚拟网络中。 Azure Functions 可以使用点到站点网关来与虚拟网络通信。 下图是此网络拓扑的示意图：

![连接到 HDInsight 的 Azure Functions 的体系结构](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>配置 Kafka

本部分中的信息对 Kafka 群集进行准备，以接受来自 Azure 函数的数据。 其中包括以下配置操作：

* IP 播发
* 收集 Kafka 代理 IP 地址
* 创建 Kafka 主题

### <a name="configure-kafka-for-ip-advertising"></a>为 IP 播发配置 Kafka

默认情况下，Zookeeper 向客户端返回 Kafka 中转站的域名。 如果没有 DNS 服务器，则此配置不起作用，因为客户端 (Azure Functions) 无法解析虚拟网络的名称。 对于此配置，请使用以下步骤来配置 Kafka，以播发 IP 地址而不是域名：

1. 使用 Web 浏览器转到 https://CLUSTERNAME.azurehdinsight.net。 将 CLUSTERNAME 替换为 Kafka on HDInsight 群集的名称。

    出现提示时，使用群集的 HTTPS 用户名称密码。 将显示群集的 Ambari Web UI。

2. 要查看 Kafka 的相关信息，请从左侧列表中选择“Kafka”。

    ![Kafka 突出显示的服务列表](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. 要查看 Kafka 配置，请在顶端的中间位置选择“配置”。

    ![Kafka 的配置链接](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. 要查找“kafka-env” 配置，请在右上方的“筛选器”字段中输入 `kafka-env`。

    ![Kafka 配置，适用于 kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

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

    ![保存配置按钮](./media/apache-kafka-azure-functions/save-button.png)

9. 要防止在重启 Kafka 时出错，请使用“服务操作”按钮，并选择“打开维护模式”。 选择“确定”完成操作。

    ![服务操作，其中已突出显示“打开维护”](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. 要重启 Kafka，请使用“重启”按钮，并选择“重启所有受影响的项”。 确认重启，在操作完成后再使用“确定”按钮。

    ![重启按钮，其中突出显示了所有受影响的重启项](./media/apache-kafka-azure-functions/restart-button.png)

11. 要禁用维护模式，请使用“服务操作”按钮，并选择“关闭维护模式”。 选择“确定”完成操作。

### <a name="get-the-kafka-broker-ip-address"></a>获取 Kafka 代理 IP 地址

使用以下方法之一检索 Kafka 群集中节点的完全限定域名 (FQDN) 和 IP 地址：

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

此命令假设 `<resourcegroupname>` 是包含虚拟网络的 Azure 资源组的名称。

在返回的名称列表中，选择某个工作节点的 IP 地址。 该节点的内部完全限定域名以字母 `wn` 开头。 函数使用此 IP 地址来与 Kafka 通信。

### <a name="create-a-kafka-topic"></a>创建 Kafka 主题

Kafka 在__主题__中存储数据。 将数据从 Azure 函数发送到 Kafka 之前，需要创建该函数。

若要创建主题，请使用[创建 Kafka 群集](apache-kafka-get-started.md)文档中的步骤。

## <a name="create-a-function-that-sends-to-kafka"></a>创建可将数据发送到 Kafka 的函数

> [!NOTE]
> 本部分中的步骤创建一个使用 [kafka-node](https://www.npmjs.com/package/kafka-node) 包将数据发布到 Kafka 的 JavaScript 函数：

1. 创建新的 __WebHook + API__ 函数，并选择“JavaScript”作为语言。 有关创建新函数的详细信息，请参阅[创建第一个函数](../../azure-functions/functions-create-first-azure-function.md#create-function)文档。

2. 使用以下代码作为函数的主体：

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    将 `'test'` 替换为在 HDInsight 群集上创建的 Kafka 主题的名称。

    将 `10.1.0.7` 替换为前面检索到的 IP 地址。 保留 `:9092` 值。 9092 是 Kafka 侦听的端口。

    使用“保存”按钮保存所做的更改。

    ![包含保存按钮的编辑器](./media/apache-kafka-azure-functions/function-editor.png)

3. 在函数编辑器的右侧，选择“查看文件”。 选择“+ 添加”并添加名为的 `package.json` 新文件。 此文件用于指定 `kafka-node` 包的依赖关系。

    ![添加文件](./media/apache-kafka-azure-functions/add-files.png)

4. 若要编辑新文件，请从“查看文件”列表中选择 `package.json`。 将以下文本用作文件的内容：

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    使用“保存”按钮保存所做的更改。

5. 若要安装 `kafka-node` 包，请参阅 [Azure Functions JavaScript 开发人员指南](../../azure-functions/functions-reference-node.md#node-version-and-package-management)的 _Node 版本和包管理_部分。

    > [!NOTE]
    > 安装 kafka-node 包后，可能会收到多个错误。 可以安全地忽略这些错误。

## <a name="run-the-function"></a>运行函数

在函数编辑器的右侧，选择“测试”。 保留默认设置进行测试，然后选择“运行”。 测试在运行时，会将 `name` 参数传递给该函数。 此参数包含由函数插入到 Kafka 中的 `Azure` 值。

![测试对话框屏幕截图](./media/apache-kafka-azure-functions/function-test-dialog.png)

若要查看运行测试时由函数记录的信息，请选择页面底部的“日志”。 再次运行测试以生成日志信息。

![函数日志输出的示例](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>验证 Kafka 中是否出现了数据

若要验证数据是否已抵达 Kafka 主题，请参阅[创建 Kafka 群集](apache-kafka-get-started.md#produce-and-consume-records)文档的_生成和使用记录_部分中的信息。 `kafka-console-consumer` 从主题读取数据，并显示主题中存储的消息列表。

## <a name="next-steps"></a>后续步骤

使用以下链接了解如何使用 Apache Kafka on HDInsight：

* [Kafka on HDInsight 入门](apache-kafka-get-started.md)

* [使用 MirrorMaker 创建 Kafka on HDInsight 的副本](apache-kafka-mirroring.md)

* [将 Apache Storm 与 Kafka on HDInsight 结合使用](../hdinsight-apache-storm-with-kafka.md)

* [将 Apache Spark 与 Kafka on HDInsight 结合使用](../hdinsight-apache-spark-with-kafka.md)

* [通过 Azure 虚拟网络连接到 Kafka](apache-kafka-connect-vpn-gateway.md)
