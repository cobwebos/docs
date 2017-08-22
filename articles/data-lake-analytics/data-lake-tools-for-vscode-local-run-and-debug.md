---
title: "Azure Data Lake 工具 - 使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试 | Microsoft Docs"
description: "了解如何使用用于 Visual Studio Code 的 Azure Data Lake 工具进行本地运行和本地调试。"
Keywords: "VScode, Azure Data Lake 工具, 本地运行, 本地调试, 预览存储文件, 上传到存储路径"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: zh-cn
ms.lasthandoff: 07/17/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试

## <a name="prerequisites"></a>先决条件
- 用于 Visual Studio Code 的 Azure Data Lake 工具。 有关说明，请参阅[使用用于 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)
- 用于 Visual Studio Code 的 C#（若要执行 U-SQL 本地调试）。

   ![安装用于 Visual Studio Code 的 Data Lake 工具 vscode.csharp](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > U-SQL 本地运行和调试功能目前仅支持 Windows 用户。 


## <a name="set-up-u-sql-local-run-environment"></a>设置 U-SQL 本地运行环境

1. 按 Ctrl+Shift+P 打开命令面板，然后输入“ADL: 下载 LocalRun 依赖项”下载程序包。  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. 从以下输出面板中显示的路径找到依赖项包，然后安装 BuildTools 并安装 Win10SDK 10240。 例如：  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- 安装 BuildTools - 请按照向导说明进行安装。   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- 安装 Win10SDK 10240 - 请按照安装说明进行安装。  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. 设置环境变量，将 SCOPE_CPP_SDK 环境变量设置为：  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
请务必重启操作系统，使环境变量设置生效。  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>启动本地运行服务并将 U-SQL 作业提交到本地帐户 
对于首次使用的用户，系统会提示“ADL: 下载 Localrun 依赖项”包（如果尚未下载）。
1. 按 Ctrl+Shift+P 打开命令面板，然后输入“ADL: 启动本地运行服务”。
2. 首次接受 EULA。 

   ![接受 EULA ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. 随即显示 cmd 控制台。 对于首次使用的用户，需要输入 3，然后输入数据输入和输出的本地文件夹路径。 对于其他选项，只需使用默认值。 

   ![用于 Visual Studio Code 的 Data Lake 工具本地运行 cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. 按 Ctrl+Shift+P 打开命令面板，然后输入“ADL: 提交作业”并选择“本地”将作业提交到本地帐户。

   ![用于 Visual Studio Code 的 Data Lake 工具选择“本地”](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. 提交作业后，可通过单击输出窗口中的 jobUrl 来查看提交详细信息。 还可从 CMD 控制台查看作业提交状态；如需了解更多作业详细信息，可在 CMD 控制台中输入 7。

   ![用于 Visual Studio Code 的 Data Lake 工具本地运行输出](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![用于 Visual Studio Code 的 Data Lake 工具本地运行 cmd 状态](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>为 U-SQL 作业启动本地调试  
对于首次使用的用户，系统会提示“ADL: 下载 Localrun 依赖项”包（如果尚未下载）。
  
1. 按 Ctrl+Shift+P 打开命令面板，然后输入“ADL: 启动本地运行服务”。 随即显示 cmd 窗口。 请确保已设置“DataRoot”。
3. 在 C# 代码隐藏中设置断点。
4. 返回脚本编辑器，按 Ctrl+Shift+P 打开命令面板，然后输入“本地调试”启动本地调试服务。

![用于 Visual Studio Code 的 Data Lake 工具本地调试结果](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>后续步骤
- 有关如何使用用于 Visual Studio Code 的 Azure Data Lake 工具，请参阅[使用用于 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)。
- 有关 Data Lake Analytics 的入门信息，请参阅[教程：Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。
- 有关如何使用用于 Visual Studio 的 Data Lake 工具的信息，请参阅[教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
- 有关开发程序集的信息，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集](data-lake-analytics-u-sql-develop-assemblies.md)。
