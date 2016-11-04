---
title: 发布 HDInsight 应用程序 | Microsoft Docs
description: 了解如何创建和发布 HDInsight 应用程序。
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2016
ms.author: jgao

---
# 将 HDInsight 应用程序发布到 Azure 应用商店中
HDInsight 应用程序是用户可以在基于 Linux 的 HDInsight 群集上安装的应用程序。这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或你自己开发的。在本文中，你将学习如何将 HDInsight 应用程序发布到 Azure 应用商店中。有关发布到 Azure 应用商店的一般信息，请参阅[将产品发布到 Azure 应用商店](../marketplace-publishing/marketplace-publishing-getting-started.md)。

HDInsight 应用程序使用*自带许可 (BYOL)* 模型，其中，应用程序提供商负责将应用程序授权给最终用户，而 Azure 只是向最终用户收取其所创建资源（例如 HDInsight 群集及其 VM/节点）的费用。目前，Azure 不经手应用程序本身的计费。

有关 HDInsight 应用程序的其他文章：

* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何将 HDInsight 应用程序安装到群集。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何安装和测试自定义 HDInsight 应用程序。

## 先决条件
若要将你的自定义应用程序提交到应用商店，必须创建并测试该自定义应用程序。请参阅以下文章：

* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何安装和测试自定义 HDInsight 应用程序。

此外，必须注册你的开发人员帐户。请参阅[将产品发布到 Azure 应用商店](../marketplace-publishing/marketplace-publishing-getting-started.md)和[创建 Microsoft 开发人员帐户](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。

## 定义应用程序
将应用程序发布到 Azure 应用商店的过程包括两个步骤。首先，定义“createUiDef.json”文件，指示你的应用程序与哪些群集兼容；然后从 Azure 门户发布模板。下面是一个示例 createUiDef.json 文件。

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| 字段 | 说明 | 可能的值 |
| --- | --- | --- |
| types |与应用程序兼容的群集类型。 |Hadoop、HBase、Storm、Spark（或这些类型的任意组合） |
| tiers |与应用程序兼容的群集层。 |Standard、Premium（或两者） |
| versions |与应用程序兼容的 HDInsight 群集类型。 |3\.4 |

## 打包应用程序
创建一个 zip 文件，其中包含安装 HDInsight 应用程序时所需的全部文件。在[发布应用程序](#publish-application)时需要此 zip 文件。

* [createUiDefinition.json](#define-application)。
* mainTemplate.json。请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)中的示例。
  
  > [!IMPORTANT]
  > 应用程序安装脚本的名称对于特定群集而言必须唯一，并采用以下格式。此外，任何安装和卸载脚本操作都应是幂等的，这意味着生成相同的结果时可重复调用这些脚本。
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > 请注意，脚本名称由三个部分组成：
  > 
  > 1. A script name prefix, which shall include either the application name or a name relevant to the application.
  > 2. A "-" for readability.
  > 3. A unique string function with the application name as the parameter.
  > 
  > An example is the above ends up becoming: hue-install-v0-4wkahss55hlas in the persisted script action list. For a sample JSON payload, see [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 
  > 
* 所有必需的脚本。

> [!NOTE]
> 应用程序文件（包括 Web 应用程序文件，如果有）可以位于任何可公开访问的终结点上。
> 
> 

## 发布应用程序
遵循以下步骤来发布 HDInsight 应用程序：

1. 登录到 [Azure 发布门户](https://publish.windowsazure.com/)。
2. 单击“解决方案模板”创建新的解决方案模板。
3. 单击“创建开发人员中心帐户并加入 Azure 计划”注册你的公司（如果尚未这样做）。请参阅[创建 Microsoft 开发人员帐户](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。
4. 单击“定义一些拓扑以开始使用”。解决方案模板是其所有拓扑的“父级”。可以在一个产品/解决方案模板中定义多个拓扑。将产品推送到过渡环境时，它会随其所有拓扑一起推送。
5. 添加新版本。
6. 上载在[包应用程序](#package-application)中准备的 zip 文件。
7. 单击“请求认证”。Microsoft 认证团队将审查该文件并认证拓扑。

## 后续步骤
* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何将 HDInsight 应用程序安装到群集。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用 Azure Resource Manager 模板在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用 Resource Manager 模板创建 HDInsight 群集。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。

<!---HONumber=AcomDC_0921_2016-->