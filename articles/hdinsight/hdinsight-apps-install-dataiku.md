---
title: 安装已发布的应用程序 - Dataiku DDS - Azure HDInsight | Microsoft Docs
description: 安装并使用 Dataiku DDS 第三方 Hadoop 应用程序。
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 835f433e0bf79e8bc4d9b30963196f65bc53cd0e
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---dataiku-dds"></a>安装已发布的应用程序 - Dataiku DDS

本文介绍如何在 Azure HDInsight 上安装和运行 [Dataiku DDS](https://www.dataiku.com/) 发布的 Hadoop 应用程序。 有关 HDInsight 应用程序平台的概述以及可用独立软件供应商 (ISV) 发布的应用程序的列表，请参阅[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

## <a name="about-dataiku-dss"></a>关于 Dataiku DSS

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/) 是一个协作性数据科学平台，可让数据科学家构建和交付分析解决方案。 借助 HDInsight 应用程序形式的 DSS 服务，可以使用数据科学来构建大数据解决方案，并以企业级的规模运行这些解决方案。

使用 DSS 可以从数据引入、准备和处理着手实施完整的分析解决方案。 在 DSS 解决方案中，还可以训练和应用机器学习模型、可视化，然后实现操作化。

可以使用 Hadoop 或 Spark 群集在 HDInsight 上安装 DSS。 可以在运行的现有群集上安装 DSS，或者在创建新群集时安装 DSS。 DSS 还支持使用 Azure Blob 存储作为连接器来读取数据。

可以使用 DSS 生成项目，然后生成 MapReduce 或 Spark 作业。 这些作业在 HDInsight 上作为常规的 MapReduce 或 Spark 作业执行，因此可以按需缩放群集。

## <a name="prerequisites"></a>先决条件

若要在新的 HDInsight 群集或现有群集上安装此应用，必须采用以下配置：

* 群集层：标准、高级
* 群集类型：Hadoop、Spark
* 群集版本：3.4、3.5

## <a name="install-the-dataiku-dss-published-application"></a>安装 Dataiku DSS 发布的应用程序

有关安装此应用程序和其他可用 ISV 应用程序的分步说明，请阅读[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。

## <a name="launch-dataiku-dss"></a>启动 Dataiku DSS

1. 安装完成后，可以通过转到 Azure 门户中的“设置”窗格，然后单击“常规”类别下的“应用程序”，从群集启动 DSS。 “已安装的应用”窗格将列出已安装的应用程序。

    ![已安装的 Dataiku DSS 应用](./media/hdinsight-apps-install-dataiku/app.png)

2. 选择“HDInsight 上的 DSS”时，会看到网页链接和 SSH 终结点路径。 选择“网页”链接。

3. 首次启动时，会看到一个用于免费创建新 Dataiku 帐户或登录到现有帐户的窗体。 还可以使用相应的选项开始免费试用[企业版](https://www.dataiku.com/dss/editions/)两周。 此时，可以使用相应的选项并输入企业版的许可密钥以继续试用，或者使用社区版。

4. 完成所选的许可证选项后，会看到一个登录窗体。 输入登录窗体前面显示的默认凭据。

以下步骤提供简单演示。

1. [下载示例 orders CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv)。

2. 在 DSS 仪表板中，选择左侧菜单中的 **+**（新建项目）链接创建新的项目。

    ![“新建项目”链接](./media/hdinsight-apps-install-dataiku/new-project.png)

3. 在“新建项目”窗体中，键入一个**名称**。 “项目密钥”中会自动填充建议的值。 对于本例，请输入“Orders”。 单击“创建”。

    ![“新建项目”窗体](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. 在“新建项目”页中选择“+ 导入第一个数据集”。

    ![文件上传](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. 选择“文件”数据集列表下的“上传文件”。 此时会显示“上传”对话框。 单击“添加文件”，选择已下载的 `haiku_shirt_sales.csv` 文件并验证。

6. 该文件随即会上传到 DSS。 单击“预览”按钮，检查 DSS 是否正确检测到了 CSV 格式。

    ![文件上传](./media/hdinsight-apps-install-dataiku/preview.png)

7. 导入过程非常完美。 CSV 文件使用制表符分隔符。 可以看到，数据采用表格格式，表格中包含称作特征的列，以及表示观测值的行。 唯一的错误表明，文件在标头与数据之间包含空行。 为了修复此错误，请在“跳过后续行”字段中输入 `1`。

    ![保存](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. 为新数据集命名。 在屏幕顶部的字段中输入 **haiku_shirt_sales**，然后选择“创建”按钮。

9. 随后会显示数据的表格视图，可在其中浏览数据。 可以看到，Dataiku Science Studio 检测到了每个列的数据类型（以蓝色显示）- 在本例中，数据类型分别为“文本”、“数字”或“日期”（未分析）。 仪表中指示了其值似乎与类型不匹配（以红色显示）或者缺失（空白）的列的比率。 在此示例数据集中，department 包含空值和无效数据。

    ![表格视图](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>数据处理

实际数据几乎总是凌乱的，很少会整齐地排列在一起。 清理数据通常需要一个脚本链和关联的业务逻辑。 Dataiku DSS 提供专用的**实验室**工具，使此任务变得更加容易。

1. 单击右上角的“实验室”。

    ![“实验室”按钮](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. 此时会打开“实验室”窗口。 在实验室中，可以迭代方式处理数据集，以便更深入地进行分析。 本教程演示视觉分析方面的功能。 选择“视觉分析”下面的“新建”按钮。 系统会提示指定分析名称。 暂时保留默认名称，然后单击“创建”。

    ![创建实验室](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. 选择页面右上角的“快速列统计信息”按钮。

    ![快速列统计信息](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. “列快速视图”窗格下面的基于时间线的图形中会显示数据类型和值的统计信息。

    ![列快速视图](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

现在，可以使用示例数据浏览 DSS。 可以清理和处理数据，以及创建新的可视化效果。

有关深度教程，请阅读[了解 Dataiku DSS](https://www.dataiku.com/learn/)。

## <a name="next-steps"></a>后续步骤

* [Dataiku DSS 文档](https://doc.dataiku.com/dss/latest/)。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure Marketplace。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试和托管 HDInsight 应用程序。
