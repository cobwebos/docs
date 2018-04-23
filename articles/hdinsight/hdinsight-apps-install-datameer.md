---
title: 安装已发布的应用程序 - Datameer - Azure HDInsight | Microsoft Docs
description: 安装并使用 Datameer 第三方 Hadoop 应用程序。
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
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 5008056ae2274d058706649f286b91b71feadc27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---datameer"></a>安装已发布的应用程序 - Datameer

本文介绍如何在 Azure HDInsight 上安装和运行 [Datameer](https://www.datameer.com/) 发布的 Hadoop 应用程序。 有关 HDInsight 应用程序平台的概述以及可用独立软件供应商 (ISV) 发布的应用程序的列表，请参阅[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

## <a name="about-datameer"></a>关于 Datameer

Datameer 是 Hadoop 平台的本机应用程序，扩展了现有的 Azure HDInsight 功能，并针对结构化和非结构化数据提供快速的集成、准备和分析。 Datameer 可以访问 70 多种源和格式：结构化、半结构化和非结构化。 可以直接上传数据，或使用其独特的数据链接按需提取数据。 Datameer 自助服务功能和用户熟悉的电子表格界面降低了大数据技术的复杂性，并加速了见解的生成。 电子表格界面提供一个简单的机制，可在其中输入声明性的公式，然后转换这些公式以优化 Hadoop 作业。 借助 Datameer、商业智能 (BI) 和 Excel 方面技能，很快就能在云中使用 Hadoop。 有关详细信息，请参阅 [Datameer 文档](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft)。

## <a name="prerequisites"></a>先决条件

若要在新的 HDInsight 群集或现有群集上安装此应用，必须采用以下配置：

* 群集层：标准
* 群集类型：Hadoop
* 群集版本：3.4

## <a name="install-the-datameer-published-application"></a>安装 Datameer 发布的应用程序

有关安装此应用程序和其他可用 ISV 应用程序的分步说明，请阅读[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。

## <a name="launch-datameer"></a>启动 Datameer

1. 安装完成后，可以通过转到 Azure 门户中的“设置”窗格，然后单击“常规”类别下的“应用程序”，从群集启动 Datameer。 “已安装的应用”窗格将列出已安装的应用程序。

    ![已安装的 Datameer 应用](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. 选择“Datameer”时，会看到网页链接和 SSH 终结点路径。 选择“网页”链接。

3. 首次启动时，可以看到两个许可证选项：14 天免费试用，或激活现有的许可证。

    ![许可证选项](./media/hdinsight-apps-install-datameer/license.png)

4. 完成所选的许可证选项后，会看到一个登录窗体。 输入登录窗体前面显示的默认凭据。 登录后，请接受软件协议以继续。

    ![登录](./media/hdinsight-apps-install-datameer/login.png)

以下步骤演示一个示“Hello World”应用。

1. [下载示例 CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf)。

2. 单击 Datameer 仪表板顶部的 **+** 符号，然后单击“文件上传”。

    ![文件上传](./media/hdinsight-apps-install-datameer/upload.png)

3. 在上传对话框中，浏览并选择已下载的 **Hello World.csv** 文件。 请确保“文件类型”设置为“CSV/TSV”。 选择“**下一步**”。 一直单击“下一步”，直到向导结束。

    ![文件上传](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. 在“新建文件夹”下将文件命名为 **Hello World**。 将新文件夹重命名为“Demo”。 选择“保存”。

    ![保存](./media/hdinsight-apps-install-datameer/save.png)

5. 再次单击 **+** 符号，并选择“工作簿”为数据创建新工作簿。

    ![添加工作簿](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. 依次展开“Data”文件夹、“FileUploads”以及保存“Hello World”文件时创建的“Demo”文件夹。 在文件列表中选择“Hello World”，然后单击“添加数据”。

    ![保存](./media/hdinsight-apps-install-datameer/select-file.png)

7. 在电子表格界面中可以看到加载的数据。 若要选择数据子集，请在工具栏中选择“筛选器”按钮。

    ![“筛选器”按钮](./media/hdinsight-apps-install-datameer/filter-button.png)

8. 在“应用筛选器”对话框中，选择“城市”列、“等于”运算符，并在筛选器文本框中键入“芝加哥”。 选中“在新工作表中创建筛选器”复选框，然后选择“创建筛选器”。

    ![应用筛选器](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. 依次单击“文件”、“保存”以保存该工作簿。 提供一个名称，例如“Hello World Workbook”。

10. 此时会显示有关如何与何时运行该工作簿的选项。 暂时保留所有选项的默认值，选中“保存后立即开始计算过程”，然后选择“保存”。

    ![保存工作簿](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer 提供强大的可视化工具。 若要显示数据，请创建信息图。 选择仪表板顶部的 **+** 符号，然后选择“信息图”。

    ![添加信息图](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. 从左侧的小组件列表中拖放条形图小组件，如下图中的步骤 1 所示。 接下来，在右侧的数据浏览器中浏览 Data 文件夹，展开工作簿，然后展开添加的包含筛选器的工作表（步骤 2）。 将条形图顶部的“名称”列拖放到“标签”目标，以将工作簿的“名称”列设置为图表的标签字段（步骤 3）。

    ![信息图](./media/hdinsight-apps-install-datameer/infographic.png)

13. 若要将“年龄”设置作为图表的 Y 轴，请将“年龄”列拖放到图表的“数据”字段中。

    ![信息图](./media/hdinsight-apps-install-datameer/infographic-age.png)

祝贺你！ 现已创建数据的可视化效果，且未编写任何代码。 接下来，可以探索不同的选项和其他可视化效果。

## <a name="next-steps"></a>后续步骤

* [Datameer 文档](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft)。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure Marketplace。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试和托管 HDInsight 应用程序。
