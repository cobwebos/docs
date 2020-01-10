---
title: Apache Kafka SSL 加密 & 身份验证-Azure HDInsight
description: 设置用于 Kafka 客户端和 Kafka 代理之间以及 Kafka 代理之间通信的 SSL 加密。 设置客户端的 SSL 身份验证。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 180b7c203755553c343e0f7fc65c93092b330124
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751327"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>设置 Azure HDInsight 中 Apache Kafka 的安全套接字层（SSL）加密和身份验证

本文介绍如何在 Apache Kafka 客户端和 Apache Kafka 代理之间设置 SSL 加密。 还介绍了如何设置客户端的身份验证（有时称为 "双向 SSL"）。

> [!Important]
> 有两个可用于 Kafka 应用程序的客户端： Java 客户端和控制台客户端。 只有 Java 客户端 `ProducerConsumer.java` 可以使用 SSL 来生成和使用。 控制台生成方客户端 `console-producer.sh` 不与 SSL 一起使用。

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Broker 安装程序

Kafka SSL 代理安装程序将按以下方式使用四个 HDInsight 群集 Vm：

* 头节点 0-证书颁发机构（CA）
* 辅助节点0、1和 2-代理

> [!Note] 
> 本指南使用自签名证书，但最安全的解决方案是使用受信任 CA 颁发的证书。

代理安装过程的摘要如下：

1. 以下步骤将在三个工作节点的每个节点上重复：

    1. 生成证书。
    1. 创建证书签名请求。
    1. 将证书签名请求发送到证书颁发机构（CA）。
    1. 登录到 CA 并为请求签名。
    1. 将已签名的证书 SCP 回辅助角色节点。
    1. SCP 向工作节点颁发 CA 的公共证书。

1. 拥有所有证书后，将证书放入证书存储区。
1. 转到 Ambari 并更改配置。

使用以下详细说明来完成代理安装：

> [!Important]
> 在以下代码片段中，wnX 是三个工作节点之一的缩写，应根据需要将其替换为 `wn0`、`wn1` 或 `wn2`。 应将 `WorkerNode0_Name` 和 `HeadNode0_Name` 替换为各自计算机的名称。

1. 在头节点0上执行初始安装，对于 HDInsight，它将填充证书颁发机构（CA）的角色。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 对每个代理执行相同的初始设置（辅助节点0、1和2）。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每个工作节点上，使用下面的代码段执行以下步骤。
    1. 创建一个密钥存储，并使用新的私有证书来填充它。
    1. 创建证书签名请求。
    1. SCP 证书签名请求到 CA （headnode0）

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. 在 CA 计算机上，运行以下命令以创建 ca 证书和 ca 密钥文件：

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. 更改为 CA 计算机并签署所有接收到的证书签名请求：

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 将已签名的证书从 CA （headnode0）发送回辅助角色节点。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 将 CA 的公共证书发送到每个辅助角色节点。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 在每个辅助角色节点上，将 Ca 公共证书添加到 truststore 和密钥存储。 然后，将辅助角色节点的签名证书添加到密钥存储

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>将 Kafka 配置更新为使用 SSL 并重启代理

现在，你已设置了包含密钥存储和 truststore 的每个 Kafka 代理，并导入了正确的证书。 接下来，请使用 Ambari 修改相关的 Kafka 配置属性，然后重启 Kafka 代理。

若要完成配置修改，请按照以下步骤操作：

1. 登录到 Azure 门户，并选择你的 Azure HDInsight Apache Kafka 群集。
1. 单击**群集仪表板**下面的“Ambari 主页”转到 Ambari UI。
1. 在“Kafka 代理”下，将 **listeners** 属性设置为 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在“高级 kafka-broker”下，将 **security.inter.broker.protocol** 属性设置为 `SSL`

    ![在 Ambari 中编辑 Kafka ssl 配置属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在“自定义 kafka-broker”下，将 **ssl.client.auth** 属性设置为 `required`。 仅在设置身份验证和加密时，才需要执行此步骤。

    ![在 Ambari 中编辑 kafka ssl 配置属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 在 "**高级 kafka** " 下，将以下行添加到**kafka 模板**属性的末尾。

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![编辑 Ambari 中的 kafka 模板属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. 重启所有 Kafka 代理。
1. 使用 "创建者" 和 "使用者" 选项启动管理客户端，验证创建者和使用者是否在端口9093上工作。

## <a name="client-setup-with-authentication"></a>客户端设置（使用身份验证）

> [!Note]
> 仅当同时设置了 SSL 加密**和**身份验证时，才需要执行以下步骤。 如果仅设置了加密，请转到[不使用身份验证的客户端设置](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

完成以下步骤以完成客户端安装：

1. 登录到客户端计算机（备用头节点）。
1. 创建 Java 密钥存储并获取代理的已签名证书。 然后将该证书复制到运行 CA 的 VM。
1. 切换到 CA 计算机（活动头节点）以对客户端证书进行签名。
1. 转到客户端计算机（备用头节点）并导航到 `~/ssl` 文件夹。 将已签名的证书复制到客户端计算机。

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (active head node) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (standby head node), navigate to ~/ssl folder and copy signed cert from the CA (active head node) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

最后，运行命令 `cat client-ssl-auth.properties`，以查看文件 `client-ssl-auth.properties`。 该文件应包含以下行：

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>客户端设置（不使用身份验证）

如果不需要身份验证，则仅设置 SSL 加密的步骤如下：

1. 登录客户端计算机 (hn1)，并转到 `~/ssl` 文件夹
1. 将已签名的证书从 CA 计算机 (wn0) 复制到客户端计算机。
1. 将 CA 证书导入信任存储
1. 将 CA 证书导入密钥存储

下面的代码片段展示了这些步骤。

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

最后，运行命令 `cat client-ssl-auth.properties`，以查看文件 `client-ssl-auth.properties`。 该文件应包含以下行：

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>后续步骤

* [什么是 Apache Kafka on HDInsight？](apache-kafka-introduction.md)
