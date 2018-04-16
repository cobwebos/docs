---
title: 使用用于 Visual Studio 的 Azure 流分析工具
description: 本文介绍如何开始使用适用于 Visual Studio 的 Azure 流分析工具。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: eaaee36a2f4dd5a313f8871cea5fd885812fe351
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>使用用于 Visual Studio 的 Azure 流分析工具
用于 Visual Studio 的 Azure 流分析工具现已推出正式版。 这些工具为流分析用户带来了更丰富的体验，可让他们进行故障排除以及编写复杂的查询，甚至可以在本地编写查询。 还可将流分析作业导出到 Visual Studio 项目中。

## <a name="introduction"></a>介绍
本教程介绍如何使用用于 Visual Studio 的流分析工具来创建、编写、本地测试、管理和调试流分析作业。 创建作业后，可使用 CI/CD Nuget 包安装 Azure 的持续集成和部署进程。若要了解详细信息，请参阅[使用流分析 VS 工具安装 CI/CD 管道](stream-analytics-tools-for-visual-studio-cicd.md)一文。

完成本教程之后，能够：

* 熟悉用于 Visual Studio 的流分析工具。
* 配置和部署流分析作业。
* 使用本地示例数据在本地测试作业。
* 使用监视功能排查问题。
* 将现有作业导出到项目。

## <a name="prerequisites"></a>先决条件
若要完成本教程，需要具备以下先决条件：

* 完成[使用流分析构建 IoT 解决方案](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)教程中的各步骤，直到“创建流分析作业”。 
* 安装 Visual Studio 2017、Visual Studio 2015 或 Visual Studio 2013 Update 4。 支持 Enterprise (Ultimate/Premium)、Professional、Community 版本。 不支持 Express 版本。 
* 请按照[安装说明](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)安装用于 Visual Studio 的流分析工具。

## <a name="create-a-stream-analytics-project"></a>创建流分析项目
在 Visual Studio 中，选择“文件” > “新建项目”。 在左侧的模板列表中，选择“流分析”，然后选择“Azure 流分析应用程序”。
像创建其他项目时一样，在页面底部输入项目“名称”、“位置”和“解决方案名称”。

![新建项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

“解决方案资源管理器”中会生成“收费亭”项目。

![解决方案资源管理器中的“收费亭”项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>选择正确的订阅
1. 在 Visual Studio 的“视图”菜单中，选择“服务器资源管理器”。

2. 使用 Azure 帐户登录。 

## <a name="define-input-sources"></a>定义输入源
1. 在“解决方案资源管理器”中展开“输入”节点，并将 Input.json 重命名为 EntryStream.json。 双击“EntryStream.json”。

2. 对于“输入别名”，输入“EntryStream”。 请注意，输入别名在查询脚本中使用。

3. 对于“源类型”，选择“数据流”。

4. 对于“源”，选择“事件中心”。

5. 对于“服务总线命名空间”，在下拉列表中选择“TollData”选项。

6. 对于“事件中心名称”，选择“入口”。

7. 对于“事件中心策略名称”，选择“RootManageSharedAccessKey”（默认值）。

8. 对于“事件序列化格式”，选择“Json”；对于“编码”，选择“UTF8”。
   
   设置如下所示：
   
   ![输入设置](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. 在页面底部，选择“保存”完成向导操作。 现在，可以添加另一个输入源来创建出口流。 右键单击“输入”节点，然后选择“新建项”。
   
   ![新建项](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. 在弹出窗口中选择“流分析输入”，将“名称”更改为 ExitStream.json。 选择 **添加** 。
   
    ![添加新项](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. 在项目中双击“ExitStream.json”，并遵循配置入口流时使用的相同步骤在各字段中填写信息。 对于“事件中心名称”，确保按以下屏幕截图所示输入“退出”：
   
    ![ExitStream 设置](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   现已定义两个输入流。
   
   ![两个输入流](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   接下来，为包含汽车注册数据的 blob 文件添加引用数据输入。
   
12. 在项目中右键单击“输入”节点，然后执行对流输入使用的相同步骤。 对于“源类型”，选择“引用数据”；对于“输入别名”，输入“注册”。
   
    ![注册设置](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. 选择包含 TollData 选项的“存储”帐户。 容器名称为 TollData，“路径模式”为 registration.json。 此文件名区分大小写，且应该全为小写。

14. 选择“保存”完成向导操作。

现已定义所有输入。

## <a name="define-output"></a>定义输出
1. 在“解决方案资源管理器”中展开“输入”节点，然后双击“Output.json”。

2. 对于“输出别名”，输入“输出”。 对于“接收器”，选择“SQL 数据库”。

3. 对于“数据库”名称，输入“TollDataDB”。

4. 对于“用户名”，输入“tolladmin”。 对于“密码”，输入“123toll!”。 对于“表”，输入“TollDataRefJoin”。

5. 选择“保存”。

   ![输出设置](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>流分析查询
本教程尝试回答几个与收费亭数据相关的业务问题。 我们构造了可在流分析中使用来提供相关答案的查询。 在开始创建第一个流分析作业之前，让我们先了解一个简单的方案和查询语法。

### <a name="introduction-to-stream-analytics-query-language"></a>流分析查询语言简介
假设需要统计进入某个收费亭的汽车数目。 由于此事件流是连续的，所以必须定义一个时段。 我们将问题修改为“每三分钟有多少汽车进入收费亭？” 这种度量方法通常称为轮转计数。

我们看看回答此问题的流分析查询：

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

如你所见，流分析使用类似 SQL 的查询语言。 它会添加一些扩展来指定查询与时间相关的方面。

有关详细信息，请参阅 MSDN 中的[时间管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)和查询中所用的[窗口化](https://msdn.microsoft.com/library/azure/dn835019.aspx)构造。

既然已经编写了第一个流分析查询，那就可以使用位于 TollApp 文件夹中以下路径的示例数据文件来测试该查询：

..\TollApp\TollApp\Data

此文件夹包含以下文件：

* Entry.json
* Exit.json
* Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>问题：进入收费亭的汽车数目
在项目中双击“Script.asaql”，即可在编辑器中打开脚本。 将上一节中的脚本粘贴到编辑器中。 查询编辑器支持 IntelliSense、语法颜色设置和错误标记。

![查询编辑器](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>在本地测试流分析查询
首先可以编译查询，查看是否存在任何语法错误。

1. 若要针对示例数据验证此查询，可以使用本地示例数据：右键单击输入并选择“添加本地输入”。
   
   ![添加本地输入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. 在弹出窗口中，从本地路径选择示例数据。 选择“保存”。
   
   ![添加本地输入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   名为 local_EntryStream.json 的文件会自动添加到输入文件夹。
   
   ![本地输入文件夹的文件列表](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. 在查询编辑器中选择“本地运行”。 或者按 F5。
   
   ![本地运行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   可从控制台输出中找到输出路径。 按任意键打开结果文件夹。
   
   ![本地运行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. 在本地文件夹中检查结果。
   
   ![本地文件夹结果](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>示例输入
还可以将输入源中的输入数据采样到本地文件。 右键单击输入配置文件并选择“示例数据”。 

![示例数据](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

请注意，目前只可对事件中心或 IoT 中心进行采样。 其他输入源不受支持。 在弹出对话框中，填写用于保存示例数据的本地路径。 选择“示例”。

![示例数据配置](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
可在“输出”窗口中查看进度。 

![示例数据输出](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>将流分析查询提交到 Azure
1. 在“查询编辑器”中，选择脚本编辑器中的“提交到 Azure”。

   ![提交到 Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. 选择“创建新的 Azure 流分析作业”。 对于“作业名称”，输入“TollApp”。 在下拉列表中选择正确的“订阅”。 选择“提交”。

   ![提交作业](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>启动作业
现已创建作业，作业视图会自动打开。 
1. 选择绿色箭头按钮启动作业。

   ![启动作业按钮](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 选择默认设置，并选择“启动”。
 
   ![启动作业](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   可以看到，作业状态已更改为“正在运行”，并出现了输入/输出事件。

   ![作业摘要和指标](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>在 Visual Studio 中检查结果
1. 打开 Visual Studio 服务器资源管理器，并右键单击“TollDataRefJoin”表。

2. 选择“显示表数据”，查看作业的输出。
   
   ![显示表数据](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>查看作业指标
“作业指标”中会显示一些基本的作业统计信息。 

![作业指标](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>在服务器资源管理器中列出作业
在“服务器资源管理器”中，选择“流分析作业”，然后选择“刷新”。 作业显示在“流分析作业”下。

![作业列表](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>打开作业视图
展开作业节点，并双击“作业视图”节点打开作业视图。

![作业视图](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>将现有作业导出到项目
可使用两种方法将现有作业导出到项目。
* 在“服务器资源管理器”中的“流分析作业”节点下，右键单击作业节点。 选择“导出到新的流分析项目”。
   
   ![导出到新的流分析项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   生成的项目会显示在“解决方案资源管理器”中。
   
    ![解决方案资源管理器作业](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* 在作业视图中，选择“生成项目”。
   
   ![生成项目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>已知问题和限制
 
* 如果查询包含地理空间函数，则本地测试无法正常工作。
* 编辑器不支持添加或更改 JavaScript UDF。
* 本地测试不支持以 JSON 格式保存输出。 
* 不支持 Power BI 输出和 ADLS 输出。



## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [使用 Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


