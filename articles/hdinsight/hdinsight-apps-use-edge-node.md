---
title: "在 HDInsight 中使用空边缘节点 | Microsoft Docs"
description: "如何将空边缘节点添加到可充当客户端的 HDInsight 群集，以及如何测试/托管 HDInsight 应用程序。"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a344a3a05c2a27e6b6500a6560c7fbb2096c9bfc
ms.openlocfilehash: 0303145ba0ce611242f218cdf6a2274e9e393fb4


---
# <a name="use-empty-edge-nodes-in-hdinsight"></a>在 HDInsight 中使用空边缘节点
了解如何将空边缘节点添加到基于 Linux 的 HDInsight 群集。 空边缘节点是安装并配置了与头节点中相同的客户端工具，但未运行 hadoop 服务的 Linux 虚拟机。 可以使用该边缘节点来访问群集、测试客户端应用程序和托管客户端应用程序。 

可以将空边缘节点添加到现有 HDInsight 群集，或者在创建群集时将此类节点添加到新群集。 添加空边缘节点的操作是使用 Azure Resource Manager 模板完成的。  以下示例演示如何使用模板执行此操作：

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

如示例中所示，可以选择性地调用[脚本操作](hdinsight-hadoop-customize-cluster-linux.md)来执行其他配置，例如，在边缘节点中安装 [Apache Hue](hdinsight-hadoop-hue-linux.md)。

创建边缘节点后，可以使用 SSH 连接到该节点，运行客户端工具访问 HDInsight 中的 Hadoop 群集。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>将边缘节点添加到现有群集
本部分介绍如何使用 Resource Manager 模板将边缘节点添加到现有 HDInsight 群集。  可以在 [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode) 中找到 Resource Manager 模板。 Resource Manager 模板调用位于 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh 的脚本操作脚本。 该脚本不执行任何操作。  此处只是演示如何从 Resource Manager 模板调用脚本操作。

**将空边缘节点添加到现有群集**

1. 创建一个 HDInsight 群集（如果没有）。  请参阅 [Hadoop 教程：开始使用 HDInsight 中基于 Linux 的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
2. 单击以下图像登录到 Azure，然后在 Azure 门户中打开 Azure Resource Manager 模板。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. 配置以下属性：
   
   * **订阅**：选择用于创建群集的 Azure 订阅。
   * **资源组**：选择用于现有 HDInsight 群集的资源组。
   * **位置**：选择现有 HDInsight 群集的位置。
   * **群集名**：输入现有 HDInsight 群集的名称。
   * **边缘节点大小**：选择一个 VM 大小。
   * **边缘节点前缀**：默认值为 **new**。  如果使用默认值，边缘节点的名称为 **new-edgenode**。  可以通过门户自定义前缀。 也可以通过模板自定义完整名称。
4. 选中“我同意上述条款和条件”，然后单击“购买”创建边缘节点。

## <a name="add-an-edge-node-when-creating-a-cluster"></a>创建群集时添加边缘节点
本部分介绍如何使用 Resource Manager 模板创建包含边缘节点的 HDInsight 群集。  可以在 [Azure 快速启动模板库](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)中找到 Resource Manager 模板。 Resource Manager 模板调用位于 https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh 的脚本操作脚本。 该脚本不执行任何操作。  此处只是演示如何从 Resource Manager 模板调用脚本操作。

**将空边缘节点添加到现有群集**

1. 创建一个 HDInsight 群集（如果没有）。  请参阅 [Hadoop 教程：开始使用 HDInsight 中基于 Linux 的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。
2. 单击以下图像登录到 Azure，然后在 Azure 门户中打开 Azure Resource Manager 模板。 
   
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
4. 选中“我同意上述条款和条件”，然后单击“购买”，以创建包含该边缘节点的群集。

## <a name="access-an-edge-node"></a>访问边缘节点
边缘节点 ssh 终结点为&lt;边缘节点名称&lt;群集名称>-ssh.azurehdinsight.cn:22。  例如，new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22。

在 Azure 门户上，边缘节点显示为应用程序。  门户中提供了使用 SSH 访问边缘节点时所需的信息。

**验证边缘节点 SSH 终结点**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开包含边缘节点的 HDInsight 群集。
3. 在群集边栏选项卡中单击“应用程序”。 此时将显示该边缘节点。  默认名称为 **new-edgenode**。
4. 单击该边缘节点。 此时将显示 SSH 终结点。

**在边缘节点上使用 Hive**

1. 使用 SSH 连接到边缘节点。  请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)或[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)。
2. 使用 SSH 连接到边缘节点后，使用以下命令打开 Hive 控制台：
   
        hive
3. 运行以下命令显示群集中的 Hive 表：
   
        show tables;

## <a name="delete-an-edge-node"></a>删除边缘节点
可以在 Azure 门户中删除边缘节点。

**访问边缘节点**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开包含边缘节点的 HDInsight 群集。
3. 在群集边栏选项卡中单击“应用程序”。 此时将显示边缘节点的列表。  
4. 右键单击要删除的边缘节点，然后单击“删除”。
5. 单击“是”确认。

## <a name="next-steps"></a>后续步骤
本文介绍了如何添加边缘节点以及如何访问边缘节点。 若要了解更多信息，请参阅下列文章：

* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何将 HDInsight 应用程序安装到群集。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure 应用商店。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用 Resource Manager 模板在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用 Resource Manager 模板创建 HDInsight 群集。




<!--HONumber=Dec16_HO3-->


