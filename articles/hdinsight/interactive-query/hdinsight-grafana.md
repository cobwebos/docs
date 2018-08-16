---
title: 在 Azure HDInsight 上使用 Grafana
description: 了解如何在 Azure HDInsight 中访问 Grafana。
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: ae8e676e9a6d6590398d6ccbb759aa29d33e108e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598137"
---
# <a name="access-grafana-in-azure-hdinsight"></a>在 Azure HDInsight 中访问 Grafana


Grafana 是一个受欢迎的开源图形和仪表板生成器。 Grafana 功能丰富；它不仅可以让用户创建可自定义和可共享的仪表板，还可提供模板化/脚本化仪表板、LDAP 集成、多个数据源等。

目前，Grafana 仅支持 Azure HDInsight 中的 Interactive Query 群集类型。


如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-hadoop-cluster"></a>创建 Hadoop 群集

本部分介绍如何使用 Azure 资源管理器模板在 HDInsight 中创建 Interactive Query 群集。 学习本文不需要有资源管理器模板方面的经验。 

1. 单击下面的“部署到 Azure”按钮以登录到 Azure，并在 Azure 门户中打开资源管理器模板。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 按以下屏幕截图所示输入或选择值：

    > [!NOTE]
    > 提供的值必须唯一，并应遵循命名指南。 模板不会执行验证检查。 如果提供的值已被使用，或不遵循指南，则提交模板后可能会出错。       
    > 
    >
    
    ![门户中的 HDInsight Linux 入门资源管理器模板](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "使用 Azure 门户和资源组管理器模板在 HDInsigut 中部署 Hadoop 群集")

    输入或选择下列值：
    
    |属性  |说明  |
    |---------|---------|
    |**订阅**     |  选择 Azure 订阅。 |
    |**资源组**     | 创建资源组，或选择现有资源组。  资源组是 Azure 组件的容器。  在本例中，资源组包含 HDInsight 群集和依赖的 Azure 存储帐户。 |
    |**位置**     | 选择要在其中创建群集的 Azure 位置。  选择的位置与你越靠近，性能就越好。 |
    |**群集类型**     | 选择“hadoop”。 |
    |**群集名称**     | 输入 Hadoop 群集的名称。 由于 HDInsight 中的所有群集共享同一 DNS 命名空间，因此该名称必须唯一。 该名称最多可以有 59 个字符，包括字母、数字和连字符。 名称的第一个和最后一个字符不能为连字符。 |
    |**群集登录名和密码**     | 默认登录名为“admin”。密码长度不得少于 10 个字符，且至少必须包含一个数字、一个大写字母和一个小写字母、一个非字母数字字符（' " ` \)字符除外）。 请确保不提供常见密码，如“Pass@word1”。|
    |**SSH 用户名和密码**     | 默认用户名为“sshuser”。  可以重命名 SSH 用户名。  SSH 用户密码的要求与群集登录密码的要求相同。|
       
    某些属性已在模板中硬编码。  可以通过模板配置这些值。 有关这些属性的详细说明，请参阅[在 HDInsight 中创建 Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)。

3. 依次选择“我同意上述条款和条件”、“固定到仪表板”，并选择“购买”。 门户仪表板上应会出现标题为“提交部署”的新磁贴。 创建群集大约需要 20 分钟时间。

    ![模板部署进度](./media/hdinsight-grafana/deployment-progress-tile.png "Azure 模板部署进度")

4. 创建群集后，该磁贴的标题将更改为指定的资源组名称。 该磁贴还列出了在资源组中创建的 HDInsight 群集。 
   
    ![HDInsight Linux 入门资源组](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Azure HDInsight 群集资源组")
    
5. 该磁贴还列出了与群集关联的默认存储。 每个群集都有一个 [Azure 存储帐户](../hdinsight-hadoop-use-blob-storage.md)或 [Azure Data Lake 帐户](../hdinsight-hadoop-use-data-lake-store.md)依赖项。 该帐户称为默认存储帐户。 HDInsight 群集与其默认存储帐户必须一起放置在同一个 Azure 区域中。 删除群集不会删除存储帐户。
    

> [!NOTE]
> 如需其他群集创建方法或要了解本教程中使用的属性，请参阅 [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md)（创建 HDInsight 群集）。       
> 
>

## <a name="access-the-grafana-dashboard"></a>访问 Grafana 仪表板

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“HDInsight 群集”，然后选择在上一部分中创建的群集名称。
3. 在“快速链接”下，单击“群集仪表板”。

    ![HDInsight 群集仪表板门户](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "门户上的 HDInsight 群集仪表板")

4. 从仪表板中，单击 **Grafana** 磁贴。
5. 输入 Hadoop 群集用户凭据。
6. Grafana 仪表板类似于：

    ![HDInsight Grafana 仪表板](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana 仪表板")

## <a name="clean-up-resources"></a>清理资源
完成本文后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。 

> [!NOTE]
> 如果立即进行下一教程，了解如何使用 Hadoop on HDInsight 运行 ETL 操作，建议保持群集运行。 这是因为该教程中必须再次创建 Hadoop 群集。 但是，如果不立即学习下一教程，则必须立即删除该群集。
> 
> 

**删除群集和/或默认存储帐户**

1. 返回到包含 Azure 门户的浏览器选项卡。 你应该在群集概览页上。 如果仅希望删除群集但保留默认的存储帐户，请选择“删除”。

    ![删除 HDInsight 群集](./media/hdinsight-grafana/hdinsight-delete-cluster.png "删除 HDInsight 群集")

2. 如果希望删除群集和默认存储帐户，请选择资源组名称（之前的屏幕截图中已突出显示），打开资源组页。

3. 选择“删除资源组”，删除资源组（包括群集和默认存储帐户）。 注意，删除资源组会删除存储帐户。 如果想要保留存储帐户，请选择仅删除群集。

## <a name="next-steps"></a>后续步骤
本教程已介绍如何使用资源管理器模板创建基于 Linux 的 HDInsight 群集，以及如何执行基本 Hive 查询。 下一篇文章将介绍如何使用 Hadoop on HDInsight 执行提取、转换和加载 (ETL) 操作。

> [!div class="nextstepaction"]
>[使用 Apache Hive on HDInsight 提取、转换和加载数据](../hdinsight-analyze-flight-delay-data-linux.md)

如果已准备好开始处理自己的数据，并需要进一步了解 HDInsight 存储数据的方式或如何将数据导入 HDInsight，请参阅以下文章：

* 有关 HDInsight 如何使用 Azure 存储的信息，请参阅[将 Azure 存储与 HDInsight 配合使用](../hdinsight-hadoop-use-blob-storage.md)。
* 若要了解如何将数据上传到 HDInsight，请参阅[将数据上传到 HDInsight](../hdinsight-upload-data.md)。

有关如何使用 HDInsight 分析数据的详细信息，请参阅以下文章：

* 若要详细了解如何将 Hive 与 HDInsight 配合使用（包括如何从 Visual Studio 中执行 Hive 查询），请参阅[将 Hive 与 HDInsight 配合使用](../hdinsight-use-hive.md)。
* 若要了解 Pig（一种用于转换数据的语言），请参阅[将 Pig 与 HDInsight 配合使用](../hdinsight-use-pig.md)。
* 若要了解 MapReduce（在 Hadoop 中处理数据的程序编写方式），请参阅[将 MapReduce 与 HDInsight 配合使用](../hdinsight-use-mapreduce.md)。
* 若要了解如何使用用于 Visual Studio 的 HDInsight 工具来分析 HDInsight 数据，请参阅 [Get started using Visual Studio Hadoop tools for HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)（用于 HDInsight 的 Visual Studio Hadoop 工具入门）。



若要详细了解如何创建或管理 HDInsight 群集，请参阅以下文章：

* 若要了解如何管理基于 Linux 的 HDInsight 群集，请参阅 [Manage HDInsight clusters using Ambari](../hdinsight-hadoop-manage-ambari.md)（使用 Ambari 管理 HDInsight 群集）。
* 有关可用于创建 HDInsight 群集的选项的详细信息，请参阅 [Creating HDInsight on Linux using custom options](../hdinsight-hadoop-provision-linux-clusters.md)（使用自定义选项在 Linux 上创建 HDInsight）。


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


