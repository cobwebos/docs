---
title: 为 Azure HDInsight 中的 Apache Kafka 设置 SSL 加密和身份验证
description: 设置 SSL 加密，以便在 Kafka 客户端与 Kafka 代理之间以及 Kafka 代理之间进行通信。 设置客户端的 SSL 身份验证。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355022"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>为 Azure HDInsight 中的 Apache Kafka 设置安全套接字层 (SSL) 加密和身份验证

本文介绍如何为 Apache Kafka 客户端与 Apache Kafka 代理之间以及 Apache Kafka 代理之间的通信设置 SSL 加密。 此外，还提供了设置客户端身份验证（有时称为双向 SSL）所要执行的步骤。

## <a name="server-setup"></a>服务器设置

第一步是在 Kafka 代理上设置密钥存储和信任存储，然后将证书颁发机构 (CA) 和代理证书导入这些存储。

> [!Note] 
> 本指南使用自签名证书，但最安全的解决方案是使用受信任 CA 颁发的证书。

1. 创建名为 ssl 的文件夹，将服务器密码导出为环境变量。 在本指南的余下内容中，请将 `<server_password>` 替换为服务器的实际管理员密码。

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. 接下来，创建 Java 密钥存储 (kafka.server.keystore.jks) 和 CA 证书。

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. 然后，创建签名请求，以获取在上一步骤中创建的、由 CA 签名的证书。

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. 现在，将签名请求发送到 CA，使此证书得到签名。 由于我们使用的是自签名证书，因此需使用 `openssl` 命令让 CA 为该证书签名。

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. 创建信任存储并导入 CA 证书。

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. 将公共 CA 证书导入密钥存储。

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. 将已签名的证书导入密钥存储。

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

将已签名的证书导入密钥存储是为 Kafka 代理配置信任存储和密钥存储所要执行的最后一个步骤。

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>将配置更新为使用 SSL 并重启代理

现已为每个 Kafka 代理设置了密钥存储和信任存储，并导入了正确的证书。  接下来，请使用 Ambari 修改相关的 Kafka 配置属性，然后重启 Kafka 代理。

1. 登录到 Azure 门户，并选择你的 Azure HDInsight Apache Kafka 群集。
1. 单击**群集仪表板**下面的“Ambari 主页”转到 Ambari UI。
1. 在“Kafka 代理”下，将 **listeners** 属性设置为 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在“高级 kafka-broker”下，将 **security.inter.broker.protocol** 属性设置为 `SSL`

    ![在 Ambari 中编辑 kafka ssl 配置属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在“自定义 kafka-broker”下，将 **ssl.client.auth** 属性设置为 `required`。 仅当同时设置了身份验证和加密时，才需要执行此步骤。

    ![在 Ambari 中编辑 kafka ssl 配置属性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 将配置属性添加到 Kafka `server.properties` 文件，以播发 IP 地址而不是完全限定的域名 (FQDN)。

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 若要验证前面所做的更改是否正确，可以选择性地检查以下行是否在 Kafka `server.properties` 文件中存在。

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. 重启所有 Kafka 代理。

## <a name="client-setup-with-authentication"></a>客户端设置（使用身份验证）

> [!Note]
> 仅当同时设置了 SSL 加密**和**身份验证时，才需要执行以下步骤。 如果仅设置了加密，请转到[不使用身份验证的客户端设置](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

1. 导出客户端密码。 将 `<client_password>` 替换为 Kafka 客户端计算机上的实际管理员密码。

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. 创建 Java 密钥存储并获取代理的已签名证书。 然后将该证书复制到运行 CA 的 VM。

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. 切换到 CA 计算机 (wn0)，以将客户端证书签名。

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. 转到客户端计算机 (hn1) 并导航到 `~/ssl` 文件夹。 将已签名的证书复制到客户端计算机。

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. 使用命令 `$cat client-ssl-auth.properties` 查看文件 `client-ssl-auth.properties`。 该文件应包含以下行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>客户端设置（不使用身份验证）

1. 导出客户端密码。 将 `<client_password>` 替换为 Kafka 客户端计算机上的实际管理员密码。

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. 转到客户端计算机 (hn1) 并导航到 `~/ssl` 文件夹。 将已签名的证书复制到客户端计算机。

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. 使用命令 `$cat client-ssl-auth.properties` 查看文件 `client-ssl-auth.properties`。 该文件应包含以下行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>后续步骤

* [什么是 Apache Kafka on HDInsight？](/../azure/hdinsight/kafka/apache-kafka-introduction)