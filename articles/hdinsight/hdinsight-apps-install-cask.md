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
ms.openlocfilehash: 9eef1760b7cee3bbdf33122514669b38b0b4d9db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>安装已发布的应用程序 - Cask Data Application Platform (CDAP)

本文介绍如何在 Azure HDInsight 上安装和运行 [CDAP](http://cask.co/products/cdap/) 发布的 Hadoop 应用程序。 有关 HDInsight 应用程序平台的概述以及可用独立软件供应商 (ISV) 发布的应用程序的列表，请参阅[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。 有关如何安装自己的应用程序的说明，请参阅[安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)。

## <a name="about-cdap"></a>关于 CDAP

在 Hadoop 中开发应用程序可能有一定的难度。  Hadoop 技术扩展的数量非常庞大且不断增多，要集成这些扩展可能需要很长时间。 监视数据流和收集指标可能需要生成独立的解决方案。

### <a name="how-does-cdap-help"></a>CDAP 有哪些作用？

Cask Data Application Platform (CDAP) 是面向大数据的集成平台。 使用 CDAP 可以专注于生成应用程序，而不是底层基础结构。

CDAP 使用开发人员非常熟悉的高级概念和抽象。 这些抽象消除了内部系统的复杂性，并可以促进解决方案的可重用性。

名为 [Cask Hydrator](http://cask.co/products/hydrator/) 的 CDAP 扩展提供一个用户界面用于开发和管理数据管道。 数据管道由各种 *插件组成，这些插件可以执行数据采集、转换、分析和运行后操作等各种任务。

每个 CDAP 插件具有妥善定义的接口，因此，评估不同的技术只需将一个插件替换为另一个插件，而无需改动应用程序的其他部分。

CDAP 管道为应用程序中的数据提供高级图形流。 此可视化效果显示数据从引入到数据转换和分析，最后再到外部数据存储的整个端到端流程。

以下示例数据管道实时引入推特数据，然后根据预定义的条件筛选出一些推文。 数据管道转换原始推文数据并将该数据投影为更容易阅读的格式，数据根据一组值分组推文，并将结果写入 HBase 存储。

![CDAP 管道](./media/hdinsight-apps-install-cask/pipeline.png)

此端到端管道是使用 **Cask Hydrator UI** 生成的，在生成过程中使用其插件接口和拖放功能在每个阶段之间建立连接。 可以单独隔离和修改每个插件的功能。 使用 CDAP，在几个小时内即可生成和验证类似的管道。 在典型的 Hadoop 领域中，构造此类解决方案可能需要几天时间。

CDAP 还提供名为 [Cask Tracker](http://cask.co/products/tracker/) 的扩展，用于直观跟踪流经应用程序的数据。 Cask Tracker 在系统中添加了“数据调控”功能，以便能够在整个应用程序中正式管理数据资产。 可以跟踪每个数据点的沿袭、收集相关指标，并审核整个过程中的数据线索。

下面演示了数据如何在上述管道中流动：

![CDAP 跟踪器](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>先决条件

若要在新的 HDInsight 群集或现有群集上安装此应用，必须采用以下配置：

* 群集层：标准
* 群集类型：HBase
* 群集版本：3.4、3.5

## <a name="install-the-cdap-published-application"></a>安装 CDAP 发布的应用程序

有关安装此应用程序和其他可用 ISV 应用程序的分步说明，请阅读[安装第三方 Hadoop 应用程序](hdinsight-apps-install-applications.md)。

## <a name="launch-cdap"></a>启动 CDAP

1. 安装完成后，可以通过转到 Azure 门户中的“设置”窗格，然后选择“常规”类别下的“应用程序”，从群集启动 CDAP。 “已安装的应用”窗格将列出所有已安装的应用程序。

    ![已安装的 CDAP 应用](./media/hdinsight-apps-install-cask/cdap-app.png)

2. 选择“CDAP”时，会看到网页链接、HTTP 终结点和 SSH 终结点路径。 选择“网页”链接。

3. 出现提示时，输入群集管理员凭据。

    ![身份验证](./media/hdinsight-apps-install-cask/auth.png)

4. 登录后，会看到 Cask CDAP GUI 主页。

    ![Cask GUI 主页](./media/hdinsight-apps-install-cask/gui.png)

5. 若要浏览 CDAP 接口，请单击页面顶部的“Cask Market”菜单链接。

    ![Cask Market 链接](./media/hdinsight-apps-install-cask/cask-market.png)

6. 在列表中选择“访问日志示例”。

    ![访问日志示例](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. 单击“加载”以确认。

    ![单击“加载”](./media/hdinsight-apps-install-cask/market-load.png)

8. 此时会显示所包含的示例数据的视图。 选择“下一步”。

    ![访问日志示例 - 查看数据](./media/hdinsight-apps-install-cask/market-view-data.png)

9. 选择“流”作为目标类型，输入目标名称，然后选择“完成”。

    ![访问日志示例 - 选择目标](./media/hdinsight-apps-install-cask/market-destination.png)

10. 成功加载数据包后，选择“查看流详细信息”。

    ![已成功上传数据包](./media/hdinsight-apps-install-cask/market-view-details.png)

11. 若要启用命名空间的元数据，请在“访问日志”详细信息页上的“用法”选项卡中选择“启用”。

    ![访问日志示例 - 已加载 - 启用元数据](./media/hdinsight-apps-install-cask/log-loaded.png)

12. 启用元数据后，会有一个图形显示审核消息信息。

    ![访问日志示例 - 已启用元数据](./media/hdinsight-apps-install-cask/log-metadata.png)

13. 若要浏览日志数据，请选择页面顶部的“浏览”图标。

    ![访问日志示例 - 浏览](./media/hdinsight-apps-install-cask/log-explore.png)

14. 将看到一个示例 SQL 查询。 请根据需要随意修改该查询，然后选择“执行”。

    ![访问日志示例 - 使用查询浏览数据集](./media/hdinsight-apps-install-cask/log-query.png)

15. 完成查询后，选择“操作”列下的“查看”图标。

    ![访问日志示例 - 查看已完成的查询](./media/hdinsight-apps-install-cask/log-query-view.png)

16. 将看到查询结果。

    ![访问日志示例 - 查询结果](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>后续步骤

* [Cask 文档](http://cask.co/resources/documentation/)。
* [安装自定义 HDInsight 应用程序](hdinsight-apps-install-custom-applications.md)：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure Marketplace。
* [MSDN：安装 HDInsight 应用程序](https://msdn.microsoft.com/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试和托管 HDInsight 应用程序。
