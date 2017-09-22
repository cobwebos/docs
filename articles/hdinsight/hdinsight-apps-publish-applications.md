---
title: "发布 Azure HDInsight 应用程序 | Microsoft Docs"
description: "了解如何创建 HDInsight 应用程序，然后在 Azure Marketplace 中进行发布。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: a7e389037a458c91d67643f7c0fca0691c22224f
ms.contentlocale: zh-cn
ms.lasthandoff: 09/09/2017

---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>在 Azure Marketplace 中发布 HDInsight 应用程序
可在基于 Linux 的 HDInsight 群集上安装 Azure HDInsight 应用程序。 在本文中，你将了解如何在 Azure Marketplace 中发布 HDInsight 应用程序。 有关在 Azure Marketplace 中发布的一般信息，请参阅[在 Azure Marketplace 中发布产品/服务](../marketplace-publishing/marketplace-publishing-getting-started.md)。

HDInsight 应用程序使用自带许可 (BYOL) 模型。 在 BYOL 方案中，应用程序提供商负责向应用用户提供应用程序许可。 应用用户仅需为其创建的 Azure 资源付费，例如 HDInsight 群集以及群集的 VM 和节点。 目前，Azure 不对应用程序本身进行计费。

有关详细信息，请参阅以下 HDInsight 应用程序相关文章：

* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md). 了解如何在群集上安装 HDInsight 应用程序。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。 了解如何安装和测试自定义 HDInsight 应用程序。

## <a name="prerequisites"></a>先决条件
若要在 Marketplace 中提交自定义应用程序，首先需[创建并测试该自定义应用程序](hdinsight-apps-install-custom-applications.md)。

还必须注册开发人员帐户。 有关详细信息，请参阅[在 Azure Marketplace 中发布产品/服务](../marketplace-publishing/marketplace-publishing-getting-started.md)和[创建 Microsoft 开发人员帐户](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。

## <a name="define-the-application"></a>定义应用程序
在 Marketplace 中发布应用程序，分为两个步骤。 首先，定义 createUiDef.json 文件。 CreateUiDef.json 文件指示应用程序与哪些群集兼容。 然后，从 Azure 门户中发布模板。 下面是一个示例 createUiDef.json 文件：

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
| versions |与应用程序兼容的 HDInsight 群集类型。 |3.4 |

## <a name="application-installation-script"></a>应用程序安装脚本
在群集（现有群集或新群集）上安装应用程序时，将创建边缘节点。 应用程序安装脚本在边缘节点上运行。

  > [!IMPORTANT]
  > 应用程序安装脚本的名称对特定群集必须唯一。 脚本名称必须具有以下格式：
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > 脚本名称有三个部分：
  > 
  > * 脚本名称前缀，必须包含应用程序名称或与应用程序相关的名称。
  > * 连字符，用于提高可读性。
  > * 一个唯一字符串函数，以应用程序名称作为参数。
  > 
  > 在持久化脚本操作列表中，上述示例显示为 hue-install-v0-4wkahss55hlas。 请参阅[示例 JSON 有效负载](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)。
  > 

安装脚本必须具有以下特点：
* 脚本是幂等的。 对脚本的多个调用生成相同的结果。
* 脚本版本控制正确。 升级或测试更改时，请使用脚本的其他位置。 这可确保安装应用程序的客户不受更新或测试影响。 
* 脚本在每个点上具有足够的日志记录。 脚本日志通常是调试应用程序安装问题的唯一方法。
* 对外部服务或资源的调用进行了充分的重试，以便安装不会受暂时性网络问题的影响。
* 如果脚本在节点上启动服务，会对该服务进行监视和配置，以便在发生节点重新启动时能够自动启动服务。

## <a name="package-the-application"></a>打包应用程序
创建一个 zip 文件，其中包含安装 HDInsight 应用程序所需的全部文件。 使用 .zip 文件来[发布应用程序](#publish-application)。 此 .zip 文件包含以下文件：

* [createUiDefinition.json](#define-application)
* mainTemplate.json（有关示例，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。）
* 所有必需的脚本

> [!NOTE]
> 可以在任何可公开访问的终结点上托管应用程序文件（包括任何 Web 应用文件）。
> 

## <a name="publish-the-application"></a>发布应用程序
要发布 HDInsight 应用程序：

1. 登录到 [Azure 发布](https://publish.windowsazure.com/)。
2. 在左侧菜单中，选择“解决方案模板”。
3. 输入标题，然后选择“创建新的解决方案模板”。
4. 如果尚未注册组织，选择“创建开发人员中心帐户并加入 Azure 计划”。  有关详细信息，请参阅[创建 Microsoft 开发人员帐户](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。
5. 选择“定义一些拓扑以开始使用”。 解决方案模板是其所有拓扑的“父级”。 可以在一个产品或解决方案模板中定义多个拓扑。 将产品/服务推送到过渡环境时，它会随其所有拓扑一起推送。 
6. 输入拓扑名称，然后选择 +。
7. 输入新版本，然后选择 +。
8. [打包应用程序](#package-application)时上传创建的 .zip 文件。  
9. 选择“请求认证”。 Microsoft 认证团队将审查该文件并认证拓扑。

## <a name="next-steps"></a>后续步骤
* 了解如何在群集中[安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)。
* 了解如何[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)并将未发布的 HDInsight 应用程序部署到 HDInsight。
* 了解如何[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)并添加更多应用程序。 
* 了解如何[使用 Azure 资源管理器模板在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。
* 了解如何[在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)，访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。


