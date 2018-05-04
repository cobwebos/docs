---
title: 在 HDInsight 中的 Hadoop 群集上使用空边缘节点 - Azure | Microsoft Docs
description: 如何将空边缘节点添加到可充当客户端的 HDInsight 群集，然后测试/托管 HDInsight 应用程序。
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: ''
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: jgao
ms.openlocfilehash: 6cb7bb982da36256707d080a7f5118127deb3a9c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中的 Hadoop 群集上使用空边缘节点

了解如何将空边缘节点添加到 HDInsight 群集。 空边缘节点是安装并配置了与头节点中相同的客户端工具，但未运行 Hadoop 服务的 Linux 虚拟机。 可以使用该边缘节点来访问群集、测试客户端应用程序和托管客户端应用程序。 

可以将空边缘节点添加到现有 HDInsight 群集，或者在创建群集时将此类节点添加到新群集。 添加空边缘节点的操作是使用 Azure 资源管理器模板完成的。  以下示例演示如何使用模板执行此操作：

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

如示例中所示，可以选择性地调用[脚本操作](hdinsight-hadoop-customize-cluster-linux.md)来执行其他配置，例如，在边缘节点中安装 [Apache Hue](hdinsight-hadoop-hue-linux.md)。 脚本操作脚本必须可在 Web 上公开访问。  例如，如果该脚本存储在 Azure 存储中，请使用公共容器或公共 blob。

边缘节点虚拟机大小必须满足 HDInsight 群集工作节点 vm 的大小要求。 有关建议的工作节点 vm 的大小信息，请参阅[在 HDInsight 中创建 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。

创建边缘节点后，可以使用 SSH 连接到该节点，运行客户端工具访问 HDInsight 中的 Hadoop 群集。

> [!WARNING] 
> 安装在空边缘节点上的自定义组件将从 Microsoft 得到商业上合理的支持。 这或许可解决遇到的问题。 或者也可参考社区资源，获取更多帮助。 以下是一些可获得社区帮助的最活跃站点：
>
> * [面向 HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com)：
>
> 如果在使用 Apache 技术，可通过 [http://apache.org](http://apache.org) 上的 Apache 项目站点（如 [Hadoop](http://hadoop.apache.org/) 站点）获取帮助。

> [!NOTE]
> 与群集一样，边缘节点也是通过修补程序托管的。  有关详细信息，请参阅[针对 HDInsight 的 OS 修补](./hdinsight-os-patching.md)。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>将边缘节点添加到现有群集
本部分介绍如何使用 Resource Manager 模板将边缘节点添加到现有 HDInsight 群集。  可以在 [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/) 中找到 Resource Manager 模板。 资源管理器模板调用位于 https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh 的脚本操作。该脚本不执行任何操作。  它只是演示如何从 Resource Manager 模板调用脚本操作。

**将空边缘节点添加到现有群集**

1. 创建一个 HDInsight 群集（如果没有）。  请参阅 [Hadoop 教程：开始使用 HDInsight 中的 Hadoop](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
2. 单击以下图像登录到 Azure，并在 Azure 门户中打开 Azure 资源管理器模板。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. 配置以下属性：
   
   * **订阅**：选择用于创建群集的 Azure 订阅。
   * **资源组**：选择用于现有 HDInsight 群集的资源组。
   * **位置**：选择现有 HDInsight 群集的位置。
   * **群集名**：输入现有 HDInsight 群集的名称。
   * **边缘节点大小**：选择一个 VM 大小。 vm 的大小必须满足工作节点 vm 的大小要求。 有关建议的工作节点 vm 的大小信息，请参阅[在 HDInsight 中创建 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。
   * **边缘节点前缀**：默认值为 **new**。  如果使用默认值，边缘节点的名称为 **new-edgenode**。  可以通过门户自定义前缀。 也可以通过模板自定义完整名称。

4. 选中“我同意上述条款和条件”，并单击“购买”创建边缘节点。

>[!IMPORTANT]
> 请确保选择现有 HDInsight 群集的 Azure 资源组。  否则，会收到错误消息“无法对嵌套资源执行请求的操作。 父资源 '&lt;ClusterName>' 未找到。”

## <a name="add-an-edge-node-when-creating-a-cluster"></a>创建群集时添加边缘节点
本部分介绍如何使用 Resource Manager 模板创建包含边缘节点的 HDInsight 群集。  可以在 [Azure 快速启动模板库](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)中找到 Resource Manager 模板。 资源管理器模板调用位于 https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh 的脚本操作。该脚本不执行任何操作。  它只是演示如何从 Resource Manager 模板调用脚本操作。

**将空边缘节点添加到现有群集**

1. 创建一个 HDInsight 群集（如果没有）。  请参阅 [开始使用 HDInsight 中的 Hadoop](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
2. 单击以下图像登录到 Azure，并在 Azure 门户中打开 Azure 资源管理器模板。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. 配置以下属性：
   
   * **订阅**：选择用于创建群集的 Azure 订阅。
   * **资源组**：创建用于该群集的新资源组。
   * **位置**：选择资源组的位置。
   * **群集名**：输入要创建的新群集的名称。
   * **群集登录用户名**：输入 Hadoop HTTP 用户名。  默认名称为 **admin**。
   * **群集登录密码**：输入 Hadoop HTTP 用户密码。
   * **SSH 用户名**：输入 SSH 用户名。 默认名称为 **sshuser**。
   * **SSH 密码**：输入 SSH 用户密码。
   * **安装脚本操作**：保留用于完成本教程的默认值。
     
     模板中已硬编码某些属性：群集类型、群集辅助角色节点计数、边缘节点大小和边缘节点名称。
4. 选中“我同意上述条款和条件”，并单击“购买”，以创建包含该边缘节点的群集。

## <a name="add-multiple-edge-nodes"></a>添加多个边缘节点

可以向一个 HDInsight 群集添加多个边缘节点。  只能使用 Azure 资源管理器模板进行多边缘节点配置。  请参阅本文开头的模板示例。  需更新 **targetInstanceCount**，使之反映要创建的边缘节点数。

## <a name="access-an-edge-node"></a>访问边缘节点
边缘节点 ssh 终结点为&lt;边缘节点名称&lt;群集名称>-ssh.azurehdinsight.cn:22。  例如，new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22。

在 Azure 门户上，边缘节点显示为应用程序。  门户中提供了使用 SSH 访问边缘节点时所需的信息。

**验证边缘节点 SSH 终结点**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开包含边缘节点的 HDInsight 群集。
3. 在群集边栏选项卡中单击“应用程序”。 此时会显示该边缘节点。  默认名称为 **new-edgenode**。
4. 单击该边缘节点。 此时会显示 SSH 终结点。

**在边缘节点上使用 Hive**

1. 使用 SSH 连接到边缘节点。 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用 SSH 连接到边缘节点后，使用以下命令打开 Hive 控制台：
   
        hive
3. 运行以下命令显示群集中的 Hive 表：
   
        show tables;

## <a name="delete-an-edge-node"></a>删除边缘节点
可以在 Azure 门户中删除边缘节点。

**访问边缘节点**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开包含边缘节点的 HDInsight 群集。
3. 在群集边栏选项卡中单击“应用程序”。 此时会显示边缘节点的列表。  
4. 右键单击要删除的边缘节点，并单击“删除”。
5. 单击“是”确认。

## <a name="next-steps"></a>后续步骤
本文介绍了如何添加边缘节点以及如何访问边缘节点。 若要了解更多信息，请参阅下列文章：

* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何将 HDInsight 应用程序安装到群集。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure Marketplace。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用 Resource Manager 模板在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用 Resource Manager 模板创建 HDInsight 群集。

