---
title: "为 Team Data Science Process 自定义 Hadoop 群集 | Microsoft Docs"
description: "使 Azure HDInsight Hadoop 群集中的常用 Python 模块可用。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0c115dca-2565-4e7a-9536-6002af5c786a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 522e33b399f2648427464b439bc4405e9e8097cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>为 Team Data Science Process 自定义 Azure HDInsight Hadoop 群集
本文介绍群集设置为 HDInsight 服务时，如何通过在每个节点上安装 64 位的 Anaconda (Python 2.7) 来自定义 HDInsight Hadoop 群集。 另外，演示如何访问头节点，将自定义作业提交到群集。 在用户指定来处理群集中的 Hive 记录的函数 (UDFs)中，自定义让 Anaconda 中许多常用的 Python 模板方便可用。 有关此方案中所用步骤的说明，请参阅 [如何提交 Hive 查询](move-hive-tables.md#submit)。

以下菜单链接到介绍如何设置各种数据科学环境（这些环境用于[团队数据科学过程 (TDSP)](overview.md)）的主题。

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

## <a name="customize"></a>自定义 Azure HDInsight Hadoop 群集
要创建自定义的 HDInsight Hadoop 群集，请先登录到 [**Azure 经典门户**](https://manage.windowsazure.com/)，单击左下角的“新建”，然后选择数据服务-> HDINSIGHT-> “自定义创建”以打开“群集详细信息”窗口。 

![创建工作区](./media/customize-hadoop-cluster/customize-cluster-img1.png)

在第 1 个配置页中输入创建的群集名称，接受其他字段的默认值。 单击箭头转到下一个配置页。 

![创建工作区](./media/customize-hadoop-cluster/customize-cluster-img1.png)

在下一页中，输入“DATA NODES”的数量，选择“REGION/VIRTUAL NETWORK”，并选择“HEAD NODE”和“DATA NODE”。 单击箭头转到下一个配置页。

> [!NOTE]
> **REGION/VIRTUAL NETWORK**必须与 HDInsight Hadoop 群集的存储帐户所用的区域相同。 否则在第 4 个配置页中，存储帐户不会出现在“帐户名”下拉列表中。
> 
> 

![创建工作区](./media/customize-hadoop-cluster/customize-cluster-img3.png)

第 3 个配置页提供 HDInsight Hadoop 群集的用户名和密码。 **不要**选择“输入 Hive/Oozie 元存储”。 然后，单击箭头转到下一个配置页。 

![创建工作区](./media/customize-hadoop-cluster/customize-cluster-img4.png)

在第 4 个配置页中，指定存储帐户名称，即 HDInsight Hadoop 群集的默认容器。 如果选择“默认容器”下拉列表中的“创建默认容器”，将创建具有相同名称的容器的群集。 单击箭头转到最后一页配置页。

![创建工作区](./media/customize-hadoop-cluster/customize-cluster-img5.png)

在最后一页“脚本操作”配置页上，单击“添加脚本操作”按钮，并使用以下值填充文本字段。

* **名称**作为此脚本操作的名称任何字符串
* **节点类型** - 选择“所有节点”
* **脚本 URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
  * *publicscripts* 是存储帐户中的公共容器 
  * *getgoing* 使用共享 PowerShell 脚本文件，以便帮助用户在 Azure 中工作
* **参数**：（将此选项留空）

最后，单击复选标记，开始创建自定义 HDInsight Hadoop 群集。 

![创建工作区](./media/customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a> 访问 Hadoop 群集的头节点
必须启用对 Azure 中的 Hadoop 群集的远程访问，才能通过 RDP 访问 Hadoop 群集的头节点。 

1. 登录到 [**Azure 经典门户**](https://manage.windowsazure.com/)，选择左侧的 **HDInsight**，从群集列表中选择Hadoop 群集，请单击 **配置** 选项卡，并单击页面底部的 **启用远程** 图标。
   
    ![创建工作区](./media/customize-hadoop-cluster/enable-remote-access-1.png)
2. 在“配置远程桌面”窗口中，输入用户名和密码字段中，并选择用于远程访问的到期日期。 然后单击复选标记，启用对 Hadoop 群集的头节点的远程访问权限。
   
    ![创建工作区](./media/customize-hadoop-cluster/enable-remote-access-2.png)

> [!NOTE]
> 远程访问的用户名和密码不是创建 Hadoop 群集时使用的用户名和密码。 这是一组单独的凭据。 此外，远程访问的到期日期必须是从当日起的 7 天内。
> 
> 

启用远程访问后，单击底部的“连接”，远程连接到头节点页。 输入先前指定的远程访问用户的凭据，登录到 Hadoop 群集的头节点。

![创建工作区](./media/customize-hadoop-cluster/enable-remote-access-3.png)

进一步的分析过程中的下一个步骤映射在[团队数据科学进程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中，并且可能包括将数据移到 HDInsight 的步骤，然后对该步骤进行采样、处理，以便使用 Azure 机器学习来学习数据。

请参阅 [如何提交 Hive 查询](move-hive-tables.md#submit)如何访问从函数 (UDFs) - 即用户指定来处理群集中的 Hive 记录的函数 - 中的群集头节点纳入 Anaconda 的 Python 模块。

