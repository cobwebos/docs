---
title: 在针对 Visual Studio Code 的 Azure Data Lake 工具中本地运行 U-SQL 并进行调试
description: 了解如何使用针对 Visual Studio Code 的 Azure Data Lake 工具本地运行和调试 U-SQL 作业。
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 765bcaab0f91e097be827bfa6e8f505ef5330d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814269"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>在 Visual Studio Code 中本地运行 U-SQL 并进行本地调试
本文介绍如何在本地开发计算机上运行 U-SQL 作业，以让早期编码阶段提速，或在 Visual Studio Code 中本地调试代码。 有关针对 Visual Studio Code 的 Azure Data Lake 工具说明，请参阅[使用针对 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)。

仅针对 Visual Studio 的 Azure Data Lake 工具的 Windows 安装支持要本地运行 U-SQL 和本地调试 U-SQL 的操作。 基于 macOS 和 Linux 操作系统上的安装不支持此功能。

## <a name="set-up-the-u-sql-local-run-environment"></a>设置 U-SQL 本地运行环境

1. 按 Ctrl + Shift + P 以打开命令面板中，然后输入**ADL:下载本地运行包**下载包。  

   ![下载 ADL LocalRun 依赖项包](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. 从“输出”面板中显示的路径找到依赖项包，并安装 BuildTools 和 Win10SDK 10240。 下面是示例路径：  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![查找依赖项包](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 若要安装 BuildTools，请单击“LocalRunDependency”文件夹中的“visualcppbuildtools_full.exe”，再按照向导说明进行操作。   

    ![安装 BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 若要安装 Win10SDK 10240，请单击“LocalRunDependency/Win10SDK_10.0.10240_2”文件夹中的 sdksetup.exe，再按照向导说明进行操作。  

    ![安装 Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. 设置环境变量。 将 SCOPE_CPP_SDK 环境变量设置为：  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>启动本地运行服务并将 U-SQL 作业提交到本地帐户 
对于第一次用户使用 **ADL:下载本地运行包** 若要下载本地运行的包，如果您还没有 [设置 U-SQL 本地运行环境](#set-up-the-u-sql-local-run-environment)。

1. 按 Ctrl + Shift + P 以打开命令面板中，然后输入**ADL:启动本地运行的服务**。   
2. 选择“接受”以接受首次安装时显示的 Microsoft 软件许可条款。 

   ![接受 Microsoft 软件许可条款](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. 随即会打开 cmd 控制台。 对于首次使用的用户，需要输入 3，并找到数据输入和输出的本地文件夹路径。 对于其他选项，可使用默认值。 

   ![针对 Visual Studio Code 的 Data Lake 工具本地运行 cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. 按 Ctrl + Shift + P 打开命令面板，请输入**ADL:提交作业**，然后选择**本地**若要将作业提交到本地帐户。

   ![针对 Visual Studio Code 的 Data Lake 工具选择“本地”](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. 提交作业后，可以查看提交详细信息。 若要查看提交详细信息，请选择“输出”窗口中的“jobUrl”。 也可以从 cmd 控制台查看作业提交状态。 如需了解更多作业详细信息，可在 cmd 控制台中输入 7。

   ![针对 Visual Studio Code 的 Data Lake 工具本地运行输出](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![针对 Visual Studio Code 的 Data Lake 工具本地运行 cmd 状态](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>为 U-SQL 作业启动本地调试  
对于初次使用的用户：

1. 使用 **ADL:下载本地运行包** 若要下载本地运行的包，如果您还没有 [设置 U-SQL 本地运行环境](#set-up-the-u-sql-local-run-environment)。
2. 安装.NET Core SDK 2.0 的建议在消息框中，如果未安装。
 
  ![提醒安装.net](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. 安装C#用于在消息框中，如果未安装 Visual Studio Code 的建议。 单击 **安装** 若要继续，然后重启 VSCode。

    ![提醒安装 C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

若要执行本地调试，请按照以下步骤进行操作：
  
1. 按 Ctrl + Shift + P 以打开命令面板中，然后输入**ADL:启动本地运行的服务**。 随即会打开 cmd 控制台。 请确保已设置 DataRoot。
2. 在 C# 代码隐藏中设置断点。
3. 返回到脚本编辑器中，右键单击并选择**ADL:本地调试**。
    
   ![针对 Visual Studio Code 的 Data Lake 工具本地调试结果](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>后续步骤
* [使用针对 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)
* [在 VS Code 中使用 Python、R 和 CSharp 开发用于 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [通过 PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 应用程序](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Data Lake Analytics(U-SQL) 目录](data-lake-analytics-use-u-sql-catalog.md)
