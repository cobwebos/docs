---
title: "使用用于 Visual Studio 的 Azure 流分析工具 | Microsoft Docs"
description: "用于 Visual Studio 的 Azure 流分析工具入门教程"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: samacha
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017

---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>使用用于 Visual Studio 的 Azure 流分析工具
## <a name="introduction"></a>介绍
本教程介绍如何使用用于 Visual Studio 的 Azure 流分析工具来创建、编写、本地测试、管理和调试流分析作业。 

完成本教程之后，你将能够：
* 熟悉用于 Visual Studio 的流分析工具。
* 配置和部署流分析作业。
* 使用本地示例数据在本地测试作业。
* 使用监视功能排查问题。
* 将现有作业导出到项目。

## <a name="prerequisites"></a>先决条件
若要完成本教程，需要具备以下先决条件：
* 完成[使用流分析构建 IoT 解决方案教程](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)中“创建流分析作业”前面的步骤。 
* 使用 Visual Studio 2015、Visual Studio 2013 Update 4 或 Visual Studio 2012。 支持 Enterprise (Ultimate/Premium)、Professional、Community 版本。 不支持 Express 版本。 不支持 Visual Studio 2017。 
* 使用用于 .NET 的 Azure SDK 2.7.1 版或更高版本。 使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)进行安装。
* 安装[用于 Visual Studio 的流分析工具](http://aka.ms/asatoolsvs)。

## <a name="create-a-stream-analytics-project"></a>创建流分析项目
1. 在 Visual Studio 中，单击“文件”菜单并选择“新建项目”。 

2. 在左侧的模板列表中，选择“流分析”，然后单击“Azure 流分析应用程序”。

3. 像创建其他项目时一样，输入项目“名称”、“位置”和“解决方案名称”。

    ![“新建项目”窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    “解决方案资源管理器”中将生成一个“收费亭”项目。

    ![解决方案资源管理器中生成的“收费亭”项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>选择正确的订阅
1. 在 Visual Studio 中，单击“视图”菜单并打开“服务器资源管理器”。

2. 使用 Azure 帐户登录。 

## <a name="define-the-input-sources"></a>定义输入源
1.  在“解决方案资源管理器”中展开“输入”节点，并将 Input.json 重命名为 EntryStream.json。 双击“EntryStream.json”。
2.  “输入别名”现为 EntryStream。 输入别名在查询脚本中使用。 
3.  在“源类型”中，选择“数据流”。
4.  在“源”中，选择“事件中心”。
5.  在“服务总线命名空间”中，选择“TollData”选项。
6.  在“事件中心名称”中，选择“入口”。
7.  在“事件中心策略名称”中，选择“RootManageSharedAccessKey”（默认值）。
8.  在“事件序列化格式”中，选择“Json”。 
9.  在“编码”中，选择“UTF8”。 设置应类似于以下屏幕截图所示：

    ![输入窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. 要完成该向导，请单击“保存”。 现在，可以添加另一个输入源来创建出口流。 右键单击“输入”节点，然后选择“新建项”。

    ![新建项](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. 在窗口中选择“Azure 流分析输入”，将“名称”更改为 ExitStream.json。 单击“添加”。

    ![添加新项窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. 在项目中双击“ExitStream.json”，然后执行对入口流使用的相同步骤。 确保按以下屏幕截图所示在“事件中心名称”中输入“退出”：

    ![ExitStream 窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    现已定义两个输入流：

    ![进入和退出输入流](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    接下来，为包含汽车注册数据的 blob 文件添加引用数据输入。

13. 在项目中右键单击“输入”节点，然后执行对流输入使用的相同步骤。 在“输入别名”中输入“注册”，在“源类型”中选择“引用数据”。

    ![“注册”窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. 在“存储帐户”中，选择“tolldata”选项。 在“容器”中选择“tolldata”，在“路径模式”中输入“registration.json”。 此文件名区分大小写，且应该全为小写。
15. 要完成该向导，请单击“保存”。

现已定义所有输入。

## <a name="define-the-output"></a>定义输出
1.  在“解决方案资源管理器”中展开“输入”节点，然后双击“Output.json”。

2.  在“输出别名”中，输入“输出”。 
3.  在“接收器”中，选择“SQL 数据库”。
4.  在“数据库”中，选择“TollDataDB”。
5.  在“用户名”中，输入“tolladmin”。 
6.  在“密码”中，输入“123toll!”。
7.  在“表”中，输入“TollDataRefJoin”。
8.  单击“保存”。

    ![输出窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>创建流分析查询
本教程尝试回答几个与收费亭数据相关的业务问题。 还构造了可在流分析中使用来提供相关答案的的流分析查询。
在开始创建第一个流分析作业之前，让我们先了解一个简单的方案和查询语法。

### <a name="introduction-to-the-stream-analytics-query-language"></a>流分析查询语言简介
假设需要统计进入某个收费亭的汽车数目。 由于此示例是一个连续事件流，所以必须定义一个时段。 将问题修改为“每三分钟有多少汽车进入收费亭？” 这种统计数据的方法通常称为轮转计数。

查看回答此问题的流分析查询：

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

流分析使用类似 SQL 的查询语言，并且添加了一些扩展以指定查询与时间相关的方面。

有关详细信息，请参阅 MSDN 中的[时间管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)和查询中所用的[窗口化](https://msdn.microsoft.com/library/azure/dn835019.aspx)构造。

现已编写了第一个流分析查询，可以开始对其进行测试。 使用位于以下路径中 TollApp 文件夹中的示例数据文件：

..\TollApp\TollApp\Data

此文件夹包含以下文件：
*   Entry.json
*   Exit.json
*   Registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>进入收费亭的汽车数量
在项目中，双击“Script.asaql”以在“查询编辑器”中打开脚本。 复制上一节中的脚本并将其粘贴到编辑器中。 查询编辑器支持 IntelliSense、语法着色和错误标记。

![查询编辑器](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>在本地测试流分析查询

1. 若要编译查询以查看是否存在语法错误，请右键单击项目并选择“生成”。 

2. 若要根据示例数据验证此查询，请使用本地示例数据。 右键单击输入，然后选择“添加本地输入”。

    ![添加本地输入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. 在弹出窗口中，从本地路径选择示例数据。 单击“保存” 。

    ![“添加本地输入”窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    名为 local_EntryStream.json 的文件将自动添加到输入文件夹。

    ![添加到输入文件夹的文件](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. 在“查询编辑器”中，单击“本地运行”。 或者可按 F5 键。

    ![本地运行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![本地运行输出](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    在 Visual Studio 中，按任意键在“ASA 本地运行结果”窗口中查看输出。 

    ![“ASA 本地运行结果”窗口](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. 单击“打开结果文件夹”，查看 CSV 和 JSON 格式的输出文件。

    ![“打开结果文件夹”输出](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>对输入数据进行采样
还可以将输入源中的输入数据采样到本地文件。 
1. 右键单击输入配置文件并选择“数据采样”。 

   ![示例数据](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    目前只可对事件中心或 IoT 中心进行采样。 其他输入源不受支持。

2. 在弹出窗口中，输入用于保存示例数据的本地路径。 单击“采样”。

    ![“数据采样”窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    可在“输出”窗口中查看进度。 

    ![输出窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>将流分析查询提交到 Azure
1. 在“查询编辑器”中，单击脚本编辑器中的“提交到 Azure”。

    ![提交到 Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. 选择“创建新的 Azure 流分析作业”。 输入“作业名称”，然后选择正确的“订阅”。 单击“提交”。

    ![“提交作业”窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>启动作业
现已创建作业，作业视图将自动打开。 
1. 若要启动作业，请单击“绿色箭头”按钮。

    ![启动作业](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 选择默认设置，然后单击“启动”。
 
    ![“启动作业”窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    作业“状态”更改为“正在运行”，并且出现“输入事件”和“输出事件”。

    ![“作业摘要”中的“正在运行”状态](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>在 Visual Studio 中检查结果
1. 在 Visual Studio 中，打开“服务器资源管理器”并右键单击“TollDataRefJoin”表。
2. 选择“显示表数据”，查看作业的输出。
   
    ![服务器资源管理器中选中“显示表数据”](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>查看作业指标
可在“作业指标”中找到一些基本的作业统计信息。 

![“作业指标”窗口](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>在服务器资源管理器中列出作业
在“服务器资源管理器”中，单击“流分析作业”，然后单击“刷新”。 作业显示在“流分析作业”下。

![服务器资源管理器中列出的流分析作业](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>打开作业视图
若要打开作业视图，请展开作业节点并双击“作业视图”节点。

![“作业视图”节点](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>将现有作业导出到项目
可使用两种方法将现有作业导出到项目。

在“服务器资源管理器”中，右键单击“流分析作业”节点中的作业节点，然后选择“导出到新的流分析项目”。

![导出到新的流分析项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

“解决方案资源管理器”中将生成项目。

![解决方案资源管理器中生成的项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
此外还可使用作业视图，并单击“生成项目”。

![生成项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>已知问题和限制
 
- 不支持 Power BI 输出和 Azure Data Lake Store 输出。
- 不支持在编辑器中添加或更改 JavaScript 用户定义函数。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [使用 Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

