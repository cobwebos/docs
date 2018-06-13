---
title: 使用 ELK 监视 Azure Service Fabric 中的应用程序 | Microsoft Docs
description: 本教程介绍如何设置 ELK 并监视 Service Fabric 应用程序。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 2c948a137abdbbf6ef8c64d26065030db1633a0a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
ms.locfileid: "29949814"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>教程：使用 ELK 监视 Service Fabric 应用程序 
本教程是一个系列中的第四部分， 其中介绍了如何使用 ELK（Elasticsearch、Logstash 和 Kibana）监视 Azure 中运行的 Service Fabric 应用程序。 

该系列的第 4 部分中介绍了如何：
> [!div class="checklist"]
> * 在 Azure 中设置 ELK 服务器
> * 将 Logstash 配置为从事件中心接收日志
> * 可视化 Kibana 中的平台和应用程序日志 

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [生成 Java Service Fabric Reliable Services 应用程序](service-fabric-tutorial-create-java-app.md)
> * [在本地群集上部署和调试应用程序](service-fabric-tutorial-debug-log-local-cluster.md)
> * [将应用程序部署到 Azure 群集](service-fabric-tutorial-java-deploy-azure.md)
> * 设置应用程序的监视和诊断
> * [设置 CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 将应用程序设置为向[第二部分](service-fabric-tutorial-debug-log-local-cluster.md)中指定的位置发出日志。
- 完成[第三部分](service-fabric-tutorial-java-deploy-azure.md)，配置一个正常运行的 Service Fabric 群集，以便向事件中心发送日志。 
- 在事件中心创建一个策略，并在其中指定“侦听”权限，以及在第三篇系列教程中创建的关联主密钥。

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序
如果未生成[本教程系列的第一部分](service-fabric-tutorial-create-java-app.md)中的投票示例应用程序，还可以下载它。 在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>在 Azure 中创建 ELK 服务器
可以在本教程中使用预配置的 ELK 环境；如果已有一个 ELK 环境，请跳到**设置 Logstash** 部分。 如果没有此环境，请遵循以下步骤在 Azure 中创建一个环境。 

1. 在 Azure 中创建由 [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) 认证的 ELK。 在本教程中，创建此服务器时不需要遵循任何特定的规范。 

2. 在 Azure 门户中转到自己的资源，然后转到“支持 + 故障排除”部分下的“启动诊断”选项卡。 接下来，单击“串行日志”选项卡。

    ![启动诊断](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. 在日志中搜索用于访问 Kibana 实例的密码。 搜索命令如以下代码片段所示：

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. 在 Azure 门户中的服务器“概述”页上按“连接”按钮，获取登录详细信息。 

    ![VM 连接](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. 使用以下命令，通过 SSH 连接到托管 ELK 映像的服务器

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER] 
    
    Example: ssh testaccount@104.40.63.157 
    ```

## <a name="set-up-elk"></a>设置 ELK 

1. 第一个步骤是加载 ELK 环境

    ```bash
    sudo /opt/bitnami/use_elk 
    ```

2. 如果使用现有的环境，则必须运行以下命令来停止 Logstash 服务

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. 运行以下命令安装适用于事件中心的 Logstash 插件。 

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. 创建或修改现有 Logstash 配置文件并在其中包含以下内容：如果是创建文件并在 Azure 中使用 ELK Bitnami 映像，则必须在 ```/opt/bitnami/logstash/conf/access-log.conf``` 位置创建该文件。 

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }
    
    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. 若要验证配置，请运行以下命令：

    ```bash 
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. 启动 Logstash 服务

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. 检查 Elasticsearch，确保可以接收数据

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. 通过 **http://SERVER-IP** 访问 Kibana 仪表板，并输入 Kibana 的用户名和密码。 如果在 Azure 中使用了 ELK 映像，则默认用户名为“user”，密码是在“启动诊断”中获取的密码。 

    ![Kibana](./media/service-fabric-tutorial-java-elk/kibana.png)    

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中启动并运行 ELK 服务器 
> * 将服务器配置为从 Service Fabric 群集接收诊断信息

转到下一教程：
> [!div class="nextstepaction"]
> [使用 Jenkins 设置 CI/CD](service-fabric-tutorial-java-jenkins.md)

