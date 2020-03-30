---
title: Apache Kafka SSL 加密和身份验证 - Azure HDInsight
description: 设置 SSL 加密，以便在 Kafka 客户端与 Kafka 代理之间以及 Kafka 代理之间进行通信。 设置客户端的 SSL 身份验证。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 4a363caf61046cf39c31ae2d5f35622b7b9109f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130002"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>为 Azure HDInsight 中的 Apache Kafka 设置安全套接字层 (SSL) 加密和身份验证

本文介绍如何在 Apache Kafka 客户端与 Apache Kafka 代理之间设置 SSL 加密。 此外还介绍了如何设置客户端的身份验证（有时称为双向 SSL）。

> [!Important]
> 有两个客户端可用于 Kafka 应用程序：一个 Java 客户端和一个控制台客户端。 只有 Java 客户端 `ProducerConsumer.java` 可以通过 SSL 来实现生成和使用。 控制台生成方客户端 `console-producer.sh` 不能与 SSL 配合工作。

> [!Note] 
> HDInsight Kafka 控制台制作器版本 1.1 不支持 SSL。
## <a name="apache-kafka-broker-setup"></a>阿帕奇卡夫卡经纪人设置

Kafka SSL 代理设置按以下方式使用四个 HDInsight 群集 VM：

* 头节点 0 - 证书颁发机构 (CA)
* 工作器节点 0、1 和 2 - 代理

> [!Note] 
> 本指南使用自签名证书，但最安全的解决方案是使用受信任 CA 颁发的证书。

代理设置过程的摘要如下：

1. 在每个工作器节点（共三个）上重复以下步骤：

    1. 生成证书。
    1. 创建证书签名请求。
    1. 将证书签名请求发送到证书颁发机构 (CA)。
    1. 登录到 CA 并为请求签名。
    1. 通过 SCP 将签名的证书发回给工作器节点。
    1. 通过 SCP 将 CA 的公共证书发送给工作器节点。

1. 获取所有证书后，将证书放入证书存储。
1. 转到 Ambari 并更改配置。

遵照以下详细说明完成代理设置：

> [!Important]
> 在以下代码片段中，wnX 是三个工作器节点中某个节点的缩写，应该相应地将其替换为 `wn0`、`wn1` 或 `wn2`。 `WorkerNode0_Name`并`HeadNode0_Name`应用相关机器的名称替换。

1. 在头节点 0 上执行初始设置，使 HDInsight 填充证书颁发机构 (CA) 的角色。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每个代理（工作器节点 0、1 和 2）上执行相同的初始设置。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每个工作器节点上，使用以下代码片段执行以下步骤。
    1. 创建密钥存储并在其中填充新的专用证书。
    1. 创建证书签名请求。
    1. 通过 SCP 将证书签名请求发送给 CA（头节点 0）

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. 在 CA 计算机上，运行以下命令创建 ca-cert 和 ca-key 文件：

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. 切换到 CA 计算机，为所有收到的证书签名请求签名：

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 将已签名的证书从 CA（头节点 0）发回给工作器节点。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 将 CA 的公共证书发送给每个工作器节点。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 在每个工作器节点上，将 CA 公共证书添加到信任存储和密钥存储。 然后，将工作器节点自身的已签名证书添加到密钥存储

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>将 Kafka 配置更新为使用 SSL 并重启代理

现在，您已经使用密钥库和信任存储为每个 Kafka 代理设置，并导入了正确的证书。 接下来，请使用 Ambari 修改相关的 Kafka 配置属性，然后重启 Kafka 代理。

若要完成配置修改，请按照以下步骤操作：

1. 登录到 Azure 门户，并选择你的 Azure HDInsight Apache Kafka 群集。
1. 单击**群集仪表板**下面的“Ambari 主页”转到 Ambari UI。****
1. 在“Kafka 代理”下，将 **listeners** 属性设置为 `PLAINTEXT://localhost:9092,SSL://localhost:9093`****
1. 在“高级 kafka-broker”下，将 **security.inter.broker.protocol** 属性设置为 `SSL`****

    ![在 Ambari 中编辑 Kafka ssl 配置属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在“自定义 kafka-broker”下，将 **ssl.client.auth** 属性设置为 `required`。**** 仅当同时设置了身份验证和加密时，才需要执行此步骤。

    ![在 Ambari 中编辑 kafka ssl 配置属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 向服务器.属性文件添加新的配置属性。

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 转到 Ambari 配置 UI 并验证新属性是否显示在**高级 kafka-env**和**kafka-env 模板**属性下。

    对于 HDI 版本 3.6：

    ![在 Ambari 中编辑“kafka-env template”属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    对于 HDI 版本 4.0：

     ![在 Ambari 四中编辑 kafka-env 模板属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. 重启所有 Kafka 代理。
1. 使用生成方和使用方选项启动管理客户端，以验证生成方和使用方是否在端口 9093 上运行。

## <a name="client-setup-without-authentication"></a>客户端设置（不使用身份验证）

如果不需要身份验证，则仅设置 SSL 加密的步骤摘要包括：

1. 登录到 CA（活动头节点）。
1. 从 CA 计算机 （wn0） 将 CA 证书复制到客户端计算机。
1. 登录到客户端计算机 （hn1） 并导航到`~/ssl`文件夹。
1. 将 CA 证书导入信任存储。
1. 将 CA 证书导入密钥存储。

这些步骤在以下代码段中进行了详细说明。

1. 登录到 CA 节点。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. 将 ca-cert 复制到客户端计算机

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. 登录到客户端计算机（备用头节点）。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. 将 CA 证书导入信任存储。

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 将 CA 证书导入密钥存储。
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 创建文件 `client-ssl-auth.properties`。 该文件应包含以下行：

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>客户端设置（使用身份验证）

> [!Note]
> 仅当同时设置了 SSL 加密**和**身份验证时，才需要执行以下步骤。 如果只是设置加密，请参阅[没有身份验证的客户端设置](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)。

以下四个步骤总结了完成客户端设置所需的任务：

1. 登录到客户端计算机（备用头节点）。
1. 创建 Java 密钥存储并获取代理的已签名证书。 然后将该证书复制到运行 CA 的 VM。
1. 切换到 CA 计算机（活动头节点）以对客户端证书进行签名。
1. 转到客户端计算机（备用头节点）并导航到`~/ssl`文件夹。 将已签名的证书复制到客户端计算机。

下面给出了每个步骤的详细信息。

1. 登录到客户端计算机（备用头节点）。

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. 删除任何现有的 ssl 目录。

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. 创建 java 密钥库并创建证书签名请求。 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. 将证书签名请求复制到 CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. 切换到 CA 计算机（活动头节点）并签署客户端证书。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. 将签名的客户端证书从 CA（活动头节点）复制到客户端计算机。

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. 将 ca-cert 复制到客户端计算机

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. 使用签名证书创建客户端存储，并将 ca 证书导入密钥库和信任存储：

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. 创建文件`client-ssl-auth.properties`。 该文件应包含以下行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>验证

> [!Note]
> 如果安装了 HDInsight 4.0 和 Kafka 2.1，则可以使用控制台制作者/使用者来验证您的设置。 如果没有，请运行端口 9092 上的 Kafka 生成器并将消息发送到主题，然后在使用 SSL 的端口 9093 上使用 Kafka 使用者。

### <a name="kafka-21-or-above"></a>卡夫卡 2.1 或以上

1. 如果主题不存在，则创建主题。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  启动控制台生成器，并提供作为`client-ssl-auth.properties`生成器的配置文件的路径。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  打开另一个连接到客户端计算机并启动控制台使用者的 ssh 连接`client-ssl-auth.properties`，并为使用者提供作为配置文件的路径。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>卡夫卡 1.1

1. 如果主题不存在，则创建主题。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  启动控制台生成器，并提供客户端-ssl-auth.属性的路径，作为生产者的配置文件。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  打开另一个连接到客户端计算机并启动控制台使用者的 ssh 连接`client-ssl-auth.properties`，并为使用者提供作为配置文件的路径。

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>后续步骤

* [什么是 Apache Kafka on HDInsight？](apache-kafka-introduction.md)
