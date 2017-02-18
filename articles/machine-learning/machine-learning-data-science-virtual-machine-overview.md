---
title: "什么是数据科学虚拟机？ | Microsoft 文档"
description: "了解关键方案、功能以及如何开始使用可用于分析的环境和工具包数据科学虚拟机。"
keywords: "数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 6c664a055e577881d4fcccd5b0ba4047d88aa9ef
ms.openlocfilehash: 64b413451c6ce47cc3aa14322b2aa0342e1e3ffe


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>适用于 Linux 和 Windows 的基于云的数据科学虚拟机简介
数据科学虚拟机是专为研究数据科学生成的 Microsoft Azure 云上的自定义 VM 映像。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。 它可用于 Windows Server 2012 或基于 OpenLogic 7.2 CentOS 的 Linux 版本。 

本主题讨论可对数据科学 VM 执行的操作、概述了使用 VM 的一些关键方案、详细列举了 Windows 和 Linux 版本上可用的关键功能，并说明了如何使用它们。

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>我如何使用数据科学虚拟机？
数据科学虚拟机的目的在于向所有技能级别和角色的数据专业人员提供无摩擦的数据科学环境。 如果已自行推出可比较的环境，此 VM 可节省大量时间。 在新创建的 VM 实例中可立即启动数据科学项目。 

数据科学 VM 的设计和配置意图在于与广泛的使用方案一起使用。 随着项目需求变化，可扩展或缩小环境。 可使用首选语言为数据科学任务编写程序。 可根据具体需要安装其他工具和自定义系统。

## <a name="key-scenarios"></a>关键方案
本部分提示一些可部署数据科学 VM 的关键方案。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>在云中预配分析桌面
数据科学 VM 为寻求使用托管的云桌面替换本地桌面的数据科学团队提供了基线配置。 此基线确保团队中的所有数据科学家具有可验证实验并促进协作的一致设置。 通过减少 sysadmin 负担并节省评估、安装和维护高级分析所需各种程序包所需的时间，它还降低了成本。  

### <a name="data-science-training-and-education"></a>数据科学训练和培训
教授数据科学课程的企业培训师和教师通常会提供虚拟机映像，确保学生设置一致，并可以预测示例运行情况。 数据科学 VM 创建可缓解支持和不兼容性挑战的一致设置的按需环境。 这些环境需要频繁生成，特别是短期培训课程的情况从中获益极大。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>对于大型项目的按需弹性容量
数据科学编程马拉松/竞赛或大型数据建模和浏览需要扩展的硬件容量，通常持续时间比较短。 数据科学 VM 可有助于在支持需要运行高性能计算资源的扩展服务器上按照需要快速复制数据科学环境。

### <a name="short-term-experimentation-and-evaluation"></a>短期实验和评估
数据科学 VM 可用于评估或学习工具，例如 Microsoft R Server、SQL Server、Visual Studio 工具、Jupyter、深度学习 / ML 工具包以及社区热门且需要最少设置量的新工具。 因为数据科学 VM 可快速设置，所以可应用于其他短期使用方案，例如复制发布的实验、执行演示、遵循在线会话或会议教程中的演练。

## <a name="whats-included-in-the-data-science-vm"></a>数据科学 VM 中包含什么内容？
数据科学虚拟机安装并配置了许多热门数据科学工具。 它还包括简化使用各种 Azure 数据和分析产品的工具。 可使用 Microsoft R Server 或 SQL Server 2016 在大型数据集上浏览和生成预测模型。 还包括开源社区和 Microsoft 的其他工具主机，示例代码和笔记本也包括在内。 下表详细列举并比较了包括在数据科学虚拟机 Windows 和 Linux 版中的主要组件。

| **Windows 版** | **Linux 版** |
| -- | --|
| 预安装了常用包的 Microsoft R Open | 预安装了常用包的 Microsoft R Open |
| 包含 MicrosoftML 算法和 Microsoft R 操作化的 Microsoft R Server Developer Edition  |Microsoft R Server Developer Edition |
| Anaconda Python 2.7、3.5 |预安装了常用包的 Anaconda Python 2.7、3.5|
| 预安装了常用包的 JuliaPro | 预安装了常用包的 JuliaPro |
| Jupyter Notebook Server（R、Python、Julia） |JupyterHub：多用户 Jupyter 笔记本（R、Python、Julia、PySpark） |
| SQL Server 2016 SP1 Developer Edition：在带有 R 服务的数据库内分析中可扩展 |PostgreSQL、SQuirreL SQL（数据库工具）、SQL Server 驱动程序和命令行（bcp、sqlcmd） |
|-  Visual Studio Community Edition 2015 (IDE) </br> - Azure HDInsight (Hadoop)、Data Lake、SQL Server Data Tools </br> - 用于 Visual Studio (RTVS 0.5) 的 Node.js、Python 和 R 工具 </br>- R Studio Desktop|IDE 和编辑器 </br> - 带有 Azure 工具包插件的 Eclipse </br> - Emacs（带有 ESS、auctex）gedit </br> - IntelliJ IDEA</br> - PyCharm</br> - Atom</br> - Visual Studio Code|
| Power BI Desktop |-- |
| 机器学习工具 </br> - 与 Azure 机器学习集成 </br> - Microsoft 认知工具包 (CNTK 2.0) 深度学习/AI </br> - Xgboost（数据科学竞赛中的热门 ML 工具） </br> - Vowpal Wabbit（快速在线学习器） </br> - Rattle（视觉快速启动数据和分析工具） </br> - Mxnet（深度学习/AI） </br> - Tensorflow  |机器学习工具 </br> - 与 Azure 机器学习集成 </br> - CNTK（深度学习/AI） </br> - Xgboost（数据科学竞赛中的热门 ML 工具） </br> - Vowpal Wabbit（快速在线学习器） </br> - Rattle（视觉快速启动数据和分析工具） </br> - Mxnet（深度学习/AI）|
| 访问服务的 Azure 和 Cortana Intelligence Suite 的 SDK |访问服务的 Azure 和 Cortana Intelligence Suite 的 SDK |
| 用于移动和管理 Azure 和大数据资源的数据的工具：Azure 存储资源管理器、CLI、PowerShell、AdlCopy (Azure Data Lake)、AzCopy、dtui（用于 DocumentDB）、Microsoft 数据管理网关 |用于移动和管理 Azure 和大数据资源的数据的工具：Azure 存储资源管理器、CLI |
| Git，Visual Studio Team Services 插件 |Git |
| 最热门 Linux/Unix 命令行实用程序的 Windows 端口可通过 GitBash/命令提示符访问 |-- |
| Weka | Weka |
| 钻取 | 钻取 |
| --| Spark local |

## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>如何使用 Windows 数据科学 VM
* 导航到[此页](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)，然后选择绿色的“创建虚拟机”按钮，在 Windows 上创建 VM 实例。
* 使用创建 VM 时指定的凭据从远程桌面登录 VM。
* 若要发现和启动可用工具，请单击“启动”菜单。

## <a name="get-started-with-the-linux-data-science-vm"></a>使用 Linux 数据科学 VM
* 导航到[此页](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)，然后选择“创建虚拟机”按钮，在 Linux（基于 OpenLogic CentOS）上创建 VM 实例。
* 使用创建 VM 时指定的凭据从 SSH 客户端（例如 Putty 或 SSH 命令）登录 VM。
* 在 Shell 提示符中，输入 dsvm-more-info。
* 对于图形桌面，请在[此处](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)下载用于客户端平台的 X2Go 客户端，并遵循 Linux 数据科学 VM 文档[预配 Linux 数据科学虚拟机](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client)中的说明操作。

## <a name="next-steps"></a>后续步骤
### <a name="for-the-windows-data-science-vm"></a>对于 Windows 数据科学 VM
* 有关如何运行 Windows 版本提供的特定工具的详细信息，请参阅[预配 Microsoft 数据科学虚拟机](machine-learning-data-science-provision-vm.md)
* 有关如何在 Windows VM 上执行数据科学项目所需的各种任务的详细信息，请参阅[数据科学虚拟机的十大功能](machine-learning-data-science-vm-do-ten-things.md)。

### <a name="for-the-linux-data-science-vm"></a>对于 Linux 数据科学 VM
* 有关如何运行 Linux 版本提供的特定工具的详细信息，请参阅[预配 Linux 数据科学虚拟机](machine-learning-data-science-linux-dsvm-intro.md)。
* 有关介绍如何通过 Linux VM 执行多个常见数据科学任务的演示，请参阅 [Linux 数据科学虚拟机上的数据科学](machine-learning-data-science-linux-dsvm-walkthrough.md)。




<!--HONumber=Feb17_HO1-->


