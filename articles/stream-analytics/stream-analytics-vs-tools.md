---
title: 在 Visual Studio 中查看 Azure 流分析作业
description: 本文介绍如何在 Visual Studio 中查看流分析作业。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 0df7c7680137be9a4b2c7c3cedb46330f4d3e5bd
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39717385"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>使用 Visual Studio 查看 Azure 流分析作业

通过针对 Visual Studio 的 Azure 流分析工具，开发人员能够直接从 IDE 中轻松管理其流分析作业。 通过 Azure 流分析工具，可以：
- [新建作业](stream-analytics-quick-create-vs.md)
- 开始、停止和[监视作业](stream-analytics-monitor-jobs-use-vs.md)
- 检查作业结果
- 将现有作业导出到项目
- 测试输入和输出连接
- [在本地运行查询](stream-analytics-vs-tools-local-run.md)

了解如何[安装针对 Visual Studio 的 Azure 流分析工具](stream-analytics-tools-for-visual-studio-install.md)。

## <a name="explore-the-job-view"></a>浏览作业视图

可使用作业视图从 Visual Studio 中与 Azure 流分析作业进行交互。

### <a name="open-the-job-view"></a>打开作业视图

1. 在“服务器资源管理器”中，选择“流分析作业”，然后选择“刷新”。 作业应显示在“流分析作业”下。

    ![流分析服务器资源管理器列表](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. 展开作业节点，并双击“作业视图”节点打开作业视图。
    
   ![展开的作业节点](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>启动和停止作业

可以从 Visual Studio 中的作业视图完全管理 Azure 流分析作业。 使用控件来启动、停止或删除作业。
    
   ![流分析作业控件](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>检查作业结果

针对 Visual Studio 的流分析工具目前支持 Azure Data Lake Storage 存储和 Blob 存储的输出预览。 若要查看结果，只需双击“作业视图”中作业关系图的输出节点并输入相应凭据即可。

   ![流分析作业 Blob 输出](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>将作业导出到项目

可使用两种方法将现有作业导出到项目。

1. 在“服务器资源管理器”中的“流分析作业”节点下，右键单击作业节点。 选择“导出到新的流分析项目”。
    
   ![将作业导出到项目](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    生成的项目会显示在“解决方案资源管理器”中。
    
   ![解决方案资源管理器](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. 在作业视图中，选择“生成项目”。
    
   ![从作业视图生成项目](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>测试连接

通过在“测试连接”下拉框中选择一个选项，可从“作业视图”中测试输入和输出连接。

   ![“测试连接”下拉框](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

“测试连接”结果会显示在“输出”窗口中。

   ![“测试连接”结果](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>后续步骤

* [使用 Visual Studio 监视和管理 Azure 流分析作业](stream-analytics-monitor-jobs-use-vs.md)
* [快速入门：使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)
* [教程：使用 VSTS 通过 CI/CD 部署 Azure 流分析作业](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [使用流分析工具进行持续集成和开发](stream-analytics-tools-for-visual-studio-cicd.md)