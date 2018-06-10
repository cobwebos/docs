---
title: 使用针对 Visual Studio Code 的 Azure Data Lake 工具
description: 了解如何使用针对 Visual Studio Code 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 4f5d51a2a34e89223f51f456f2c730835b1e2451
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735181"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>使用针对 Visual Studio Code 的 Azure Data Lake 工具

了解如何使用针对 Visual Studio Code (VS Code) 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。 这些信息也包含在以下视频中：

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>先决条件

适用于 VSCode 的 Azure Data Lake 工具支持 Windows、Linux 和 MacOS。  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。

对于 MacOS 和 Linux：
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)。 
- [Mono 5.2.x](http://www.mono-project.com/download/)。

## <a name="install-azure-data-lake-tools"></a>安装 Azure Data Lake 工具

安装必备组件后，即可安装用于 VS Code 的 Azure Data Lake 工具。

**安装 Azure Data Lake 工具的步骤**

1. 打开 Visual Studio Code。
2. 单击左侧窗格中的“扩展”。 在搜索框中输入“Azure Data Lake 工具”。
3. 单击“Azure Data Lake 工具”旁边的“安装”。 数秒后，“安装”按钮将变为“重载”。
4. 单击“重载”，激活“Azure Data Lake 工具”扩展。
5. 单击“重载窗口”以进行确认。 在“扩展”窗格中，可以看到“Azure Data Lake 工具”。

    ![“针对 Visual Studio Code 的 Data Lake 工具扩展”窗格](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>激活 Azure Data Lake 工具
创建新的 .usql 文件，或打开现有的 .usql 文件以激活扩展。 


## <a name="work-with-u-sql"></a>处理 U-SQL

若要处理 U-SQL，需打开 U-SQL 文件或文件夹。

**打开示例脚本的步骤**

打开命令面板 (Ctrl+Shift+P)，并输入 **ADL: Open Sample Script**。 这会打开此示例的另一个实例。 还可以对此实例编辑、配置和提交脚本。

**打开 U-SQL 项目文件夹的方法**

1. 在 Visual Studio Code 中，选择“文件”菜单，并选择“打开文件夹”。
2. 指定文件夹，并选择“选择文件夹”。
3. 选择“文件”菜单，并选择“新建”。 一个 Untilted-1 文件会添加到项目。
4. 在 Untitled-1 文件中输入以下代码：

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    脚本创建具有 /output 文件夹中某些数据的 departments.csv 文件。

5. 在打开的文件夹中，将文件命名为 **myUSQL.usql**，并保存。

**编译 U-SQL 脚本的方法**

1. 按 Ctrl+Shift+P 打开命令面板。 
2. 输入“ADL: Compile Script”。 编译结果显示在“输出”窗口中。 也可以右键单击脚本文件，并选择“ADL: Compile Script”编译 U-SQL 作业。 编译结果显示在“输出”窗格中。
 
**提交 U-SQL 脚本的方法**

1. 按 Ctrl+Shift+P 打开命令面板。 
2. 输入“ADL: Submit Job”。  也可以右键单击脚本文件，并选择“ADL: Submit Job”。 

 提交 U-SQL 作业后，提交日志会显示在 VS Code 的“输出”窗口中。 作业视图将显示在右侧窗格中。 如果提交成功，还会显示作业 URL。 可以在 Web 浏览器中打开作业 URL，跟踪实时作业状态。 在“作业视图摘要”选项卡上，可以看到作业详细信息。 主要功能包括重新提交脚本、复制脚本、在门户中打开。 在“作业视图数据”选项卡上，可以参考输入文件、输出文件、资源。 可将文件下载到本地计算机。

   ![用于 Visual Studio Code 的 Data Lake 工具配置文件](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![用于 Visual Studio Code 的 Data Lake 工具配置文件](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**设置默认上下文**

 可设置要中，如果没有为文件分别设置参数，可将要应用此设置的默认上下文设置为所有脚本文件。

1. 按 Ctrl+Shift+P 打开命令面板。 
2. 输入“ADL: Set Default Context”。
3. 或者右键单击脚本编辑器，选择“ADL: Set Default Context”，然后选择所需的帐户、数据库和架构。 设置将保存到 xxx_settings.json 配置文件。

    ![用于 Visual Studio Code 的 Data Lake 工具配置文件](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**设置脚本参数**

1. 按 Ctrl+Shift+P 打开命令面板。 
2. 输入“ADL: Set Script Parameters”。
3. 将打开 xxx_settings.json，其中显示了以下属性：

  - 帐户：在 Azure 订阅中需要编译和运行 U-SQL 作业的 Data Lake Analytics 帐户，因此，你需要在编译和运行 U-SQL 作业之前配置计算机帐户。
    - 数据库：你帐户下的数据库。 默认为 **master**。
    - 架构：你数据库下的架构。 默认为 **dbo**。
    - 可选设置：
        - 优先级：优先级范围是 1 到 1000，1 是最高优先级。 默认值为 **1000**。
        - 并行度：并行度范围是 1 到 150。 默认值是 Azure Data Lake Analytics 帐户中允许的最大并行度。 

        ![用于 Visual Studio Code 的 Data Lake 工具配置文件](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > 如果未设置默认上下文，则保存配置后，帐户、数据库和架构信息会显示在对应 .usql 文件左下角的状态栏上。

**Set Git Ignore**

1. 按 Ctrl+Shift+P 打开命令面板。 
2. 输入 **ADL:  Set Git Ignore**。

    - 如果 VSCode 工作文件夹中没有 **.gitIgnore** 文件，则会在该文件夹中创建一个名为 **.gitIgnor** 的文件。 默认情况下会将四个项（**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、**.cache** 和 **obj**）添加到该文件。 如果需要，可以进一步进行更新。
    - 如果 VSCode 工作文件夹中已有一个 **.gitIgnore** 文件，则该工具会向 **.gitIgnore** 文件中添加四个项（**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、**.cache**、**obj**）（如果该文件中尚未包括这四个项）。

    ![用于 Visual Studio Code 的 Data Lake 工具配置文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-file-csharp-python-and-r"></a>处理代码隐藏文件：CSharp、Python 和 R

Azure Data Lake 工具支持多个自定义代码，有关说明，请参阅[在 VSCode 中使用 Python、R 和 CSharp 开发用于 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)。

## <a name="work-with-assemblies"></a>处理程序集

有关开发程序集的信息，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集](data-lake-analytics-u-sql-develop-assemblies.md)。

可以使用 Data Lake 工具在 Data Lake Analytics 目录中注册自定义代码程序集。

**注册程序集的方法**

可以通过 ADL: Register Assembly 或 ADL: Register Assembly (Advanced) 命令注册程序集。

**通过 ADL: Register Assembly 命令注册**
1.  按 Ctrl+Shift+P 打开命令面板。
2.  输入 **ADL: Register Assembly**。 
3.  指定本地程序集路径。 
4.  选择 Data Lake Analytics 帐户。
5.  选择数据库。

结果：门户在浏览器中打开并显示程序集注册过程。  

触发 **ADL: Register Assembly** 命令的另一种便利方法是在文件资源管理器中右键单击 .dll 文件。 

**通过 ADL: Register Assembly (Advanced) 注册**
1.  按 Ctrl+Shift+P 打开命令面板。
2.  输入“ADL: Register Assembly (Advanced)”。 
3.  指定本地程序集路径。 
4.  会显示 JSON 文件。 检查并根据需要编辑程序集依赖项和资源参数。 指令会显示在“输出”窗口中。 若要继续进行程序集注册，请保存 (Ctrl+S) JSON 文件。

    ![针对 Visual Studio Code 的 Data Lake 工具代码隐藏](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- 程序集依赖项：Azure Data Lake 工具会自动检测 DLL 是否具有任何依赖项。 依赖项在检测到之后会显示在 JSON 文件中。 
   >- 资源：可以在程序集注册过程中上传 DLL 资源（例如，.txt、.png 和 .csv）。 

触发 ADL: Register Assembly (Advanced) 命令的另一种方法是在文件资源管理器中右键单击 .dll 文件。 

以下 U-SQL 代码演示如何调用程序集。 在示例中，程序集名称是 test。


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="u-sql-local-run-and-local-debug-for-windows-users"></a>Windows 用户的 U-SQL 本地运行和本地调试
在将代码发布到 Data Lake Analytics 之前，U-SQL 本地运行可测试本地数据并在本地验证脚本。 使用本地调试功能可以在将代码提交到 Data Lake Analytics 之前完成以下任务： 
- 调试 C# 代码隐藏。 
- 逐步执行代码。 
- 在本地验证脚本。

有关本地运行和本地调试的说明，请参阅[使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)。


## <a name="connect-to-azure"></a>连接到 Azure

必须首先接到 Azure 帐户，才能在 Data Lake Analytics 中编译和运行 U-SQL 脚本。

<b id="sign-in-by-command">使用命令连接到 Azure 的步骤</b>

1.  按 Ctrl+Shift+P 打开命令面板。 
2.  输入 **ADL: Login**。 登录信息显示在右下角。

    ![用于 Visual Studio Code 的 Data Lake 工具命令面板](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![用于 Visual Studio Code 的 Data Lake 工具设备登录信息](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  单击“复制并打开”，通过 URL https://aka.ms/devicelogin 打开登录网页。 将代码粘贴到文本框中，然后选择“继续”。

    ![用于 Visual Studio Code 的 Data Lake 工具登录粘贴代码](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )  
     
4.  按照说明从网页登录。 连接后，“VS Code”窗口左下角的状态栏上会显示 Azure 帐户名称。 

    > [!NOTE] 
    >- 如果之前已经登录但尚未注销，则 Data Lake Tool 下次会自动登录。
    >- 如果帐户已启用双重身份验证，我们建议使用手机身份验证，而不要使用 PIN 码。


若要注销，请输入命令 **ADL: Logout**

**从浏览器连接到 Azure 的步骤**

若要从浏览器登录，请展开“AZURE DATALAKE”，单击“登录到 Azure...”，然后按照[**使命命令连接到 Azure 的步骤**](#sign-in-by-command)中的步骤 3 和步骤 4 进行操作。

![从浏览器连接到 Azure](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

不能从浏览器注销。 若要注销，请参阅[**使用命令连接到 Azure 的步骤**](#sign-in-by-command)。


## <a name="create-extract-script"></a>创建 EXTRACT 脚本 
可以通过命令“ADL: 创建 EXTRACT 脚本”或 **AZURE DATALAKE** 资源管理器为 .csv、.tsv、.txt 文件创建 EXTRACT 脚本。

**使用命令创建 EXTRACT 脚本的步骤**

1. 按 Ctrl+Shift+P 打开命令面板，然后输入“ADL: 创建 EXTRACT 脚本”。
2. 指定某个 Azure 存储文件的完整路径，然后按 **Enter**。
3. 选择一个帐户。
4. 对于 .txt 文件，请选择一个用于提取文件的分隔符。 

    ![创建 EXTRACT 脚本过程](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

EXTRACT 脚本是根据条目生成的。 对于不能检测列的脚本，请从两个选项中选择一个。 否则，只会生成一个脚本。

![创建 EXTRACT 脚本结果](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**从浏览器创建 EXTRACT 脚本的步骤**

另一种创建 EXTRACT 脚本的方法是使用 Data Lake 存储或 Blob 存储的 .csv、.tsv、.txt 文件中的右键菜单。

![从上下文菜单创建 EXTRACT 脚本](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-command"></a>通过命令与 Azure Data Lake Analytics 集成

可以访问 Azure Data Lake Analytics 资源，包括列出帐户、访问元数据、查看分析作业。 

**列出 Azure 订阅下 Azure Data Lake Analytics 帐户的步骤**

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 **ADL: List Accounts**。 帐户随即显示在“输出”窗格中。

**访问 Azure Data Lake Analytics 元数据**

1.  按 Ctrl+Shift+P，并输入 **ADL: List Tables**。
2.  选择其中一个 Data Lake Analytics 帐户。
3.  选择其中一个 Data Lake Analytics 数据库。
4.  选择其中一个架构。 可以看到表的列表。

**查看 Azure Data Lake Analytics 作业的步骤**
1.  打开命令面板 (Ctrl+Shift+P)，并选择“ADL: Show Jobs”。 
2.  选择 Data Lake Analytics 或本地帐户。 
3.  等待显示帐户的作业列表
4.  从作业列表中选择作业，Data Lake 工具将在右侧窗格中打开作业视图，并在 VS 代码输出中显示一些信息。

    ![用于 Visual Studio Code 的 Data Lake 工具 IntelliSense 对象类型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-storage-through-command"></a>通过命令与 Azure Data Lake 存储集成

可以使用 Azure Data Lake 存储相关的命令来执行以下操作：
 - 浏览 Azure Data Lake 存储资源。 [列出存储路径](#list-the-storage-path)。 
 - 预览 Azure Data Lake 存储文件。 [预览存储文件](#preview-the-storage-file)。 
 - 在 VS Code 中将文件上传到 Azure Data Lake 存储。 [上传文件或文件夹](#upload-file-or-folder)。
 - 直接从 VS Code 的 Azure Data Lake 存储下载文件。 [下载文件](#download-file)。

### <a name="list-the-storage-path"></a>列出存储路径 

**通过命令面板列出存储路径**

右键单击脚本编辑器，然后选择“ADL: 列出路径”。

选择列表中的文件夹，也可以单击“输入路径”或“从根目录浏览”（以“输入路径”为例）。 -> 选择 ADLA 帐户。 -> 转到或输入存储文件夹路径（例如，/output/）。 -> 命令面板根据输入内容列出路径信息。

![针对 Visual Studio Code 的 Data Lake 工具列出存储路径结果](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

列出相对路径的一种更便利方法是右键单击上下文菜单。

**通过右键单击列出存储路径**

右键单击路径字符串，选择“列出路径”即可继续。

![针对 Visual Studio Code 的 Data Lake 工具右键单击上下文菜单](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>预览存储文件

右键单击脚本编辑器，然后选择“ADL: 预览文件”。

选择 ADLA 帐户。 -> 输入 Azure 存储文件路径（例如，/output/SearchLog.txt）。 -> 结果就是，文件在 VSCode 中打开。

   ![用于 Visual Studio Code 的 Data Lake 工具预览文件结果](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

预览文件的另一种方法是，通过在脚本编辑器的文件完整路径或文件相对路径上右键单击菜单。 

### <a name="upload-file-or-folder"></a>上传文件或文件夹

1. 右键单击脚本编辑器，然后选择“上传文件”或“上传文件夹”。

2. 如果选择“上传文件”，则选择一个或多个文件，如果选择“上传文件夹”，则选择整个文件夹，然后单击“上传”。 -> 选择列表中的存储文件夹，也可以单击“输入路径”或“从根目录浏览”（以“输入路径”为例）。 -> 选择 ADLA 帐户。 -> 转到或输入存储文件夹路径（例如，/output/）。 -> 单击“选择当前文件夹”，指定上传目标。

   ![针对 Visual Studio Code 的 Data Lake 工具上传状态](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   将文件上传到存储的另一种方法是，通过脚本编辑器中文件完整路径或文件相对路径的右键单击菜单。

与此同时，可以监视[上传状态](#check-storage-tasks-status)。


### <a name="download-file"></a>下载文件 
可以通过命令“ADL: 下载文件”或“ADL: 下载文件(高级)”来下载文件。

**通过“ADL: 下载文件(高级)”下载文件的具体步骤**
1. 右键单击脚本编辑器，然后选择“下载文件(高级)”。
2. VS Code 显示 JSON 文件。 可以输入文件路径，以便同时下载多个文件。 指令会显示在“输出”窗口中。 若要继续下载文件，请保存（按 Ctrl+S）JSON 文件。

    ![针对 Visual Studio Code 的 Data Lake 工具使用 config 下载文件](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  结果：“输出”窗口显示文件上传状态。

    ![结果：针对 Visual Studio Code 的 Data Lake 工具下载多个文件](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

与此同时，可以监视[下载状态](#check-storage-tasks-status)。

**通过“ADL:下载文件”下载文件的具体步骤**

1. 右键单击脚本编辑器，选择“下载文件”，然后从“选择文件夹”对话框选择目标文件夹。

2. 选择列表中的文件夹，也可以单击“输入路径”或“从根目录浏览”（以“输入路径”为例）。 -> 选择 ADLA 帐户。 -> 转到或输入存储文件夹路径（例如，/output/）-> 选择要下载的文件。

   ![针对 Visual Studio Code 的 Data Lake 工具的下载状态](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   下载存储文件的另一种方法是，通过脚本编辑器中文件完整路径或文件相对路径的右键单击菜单。

与此同时，可以监视[下载状态](#check-storage-tasks-status)。

### <a name="check-storage-tasks-status"></a>检查存储任务的状态
完成下载和上传时，状态显示在状态栏底部。
1. 单击下面的状态栏，即可在“输出”面板中查看下载和上传状态。

   ![针对 Visual Studio Code 的 Data Lake 工具检查存储状态](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-explorer"></a>通过资源管理器与 Azure Data Lake Analytics 集成

- 登录后，你将看到你的 Azure 帐户下的所有订阅都在“AZURE DATALAKE”的左侧面板中列出。 

   ![DataLake 资源管理器](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**ADLA 元数据导航**

- 展开你的 Azure 订阅，可以在 U-SQL 数据库中导航，在“U-SQL 数据库”节点下查看**架构**、**凭据**、**程序集**、**表**、**索引**等等。

**ADLA 元数据实体管理**

- 展开“U-SQL 数据库”，可以通过在相应的节点下右键单击“用于创建的脚本”来创建新的数据库、架构、表、表类型、索引和统计信息。 在打开的脚本页中，根据需要编辑脚本，然后通过右键单击上下文菜单“ADL: 提交作业”来提交作业。 完成创建后，单击上下文菜单“刷新”来显示新创建的项。 还可以通过右键单击上下文菜单“删除”来删除该项。

   ![DataLake 资源管理器“创建新项”菜单](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![DataLake 资源管理器“创建新项”脚本](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**ADLA 程序集注册**

 - 可以通过右键单击“程序集”在相应的数据库中**注册程序集**。

    ![DataLake 资源管理器](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-storage-from-explorer"></a>通过资源管理器与 Azure Data Lake 存储集成

导航到“Data Lake Store”

 - 在文件夹节点上，可以在右键单击的上下文菜单中执行“刷新”、“删除”、“上传”、“上传文件夹”、“复制相对路径”、“复制完整路径”操作。

   ![DataLake 资源管理器](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- 在文件节点上，可以在右键单击的上下文菜单中执行“预览”、“下载”、“删除”、“创建 EXTRACT 脚本”（仅适用于 CSV、TSV 和 TXT 文件）、“复制相对路径”、“复制完整路径”操作。

    ![DataLake 资源管理器 - 提取](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-explorer"></a>通过资源管理器与 Azure Blob 存储集成

导航到“Blob 存储”

- 在 Blob 容器节点上，可以在右键单击的上下文菜单中执行“刷新”、“删除 Blob 容器”、“上传 Blob”操作。

    ![Blob 存储 blob 容器节点](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- 在文件夹节点上，可以在右键单击的上下文菜单中执行“刷新”和“上传 Blob”操作。

    ![Blob 存储文件夹节点](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- 在文件节点上，可以在右键单击的上下文菜单中执行“预览/编辑”、“下载”、“删除”、“创建 EXTRACT 脚本”（仅适用于 CSV、TSV 和 TXT 文件）、“复制相对路径”、“复制完整路径”操作。

    ![从上下文菜单创建 EXTRACT 脚本](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-adl-storage-explorer-in-portal"></a>在门户中打开 ADL 存储资源管理器
1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 **Open Web Azure Storage Explorer**，或在脚本编辑器中的相对路径或完整路径上右键单击，并选择“打开 Web Azure 存储资源管理器”。
3. 选择 Data Lake Analytics 帐户。

Data Lake 工具会在 Azure 门户中打开 Azure 存储路径。 可以从 Web 查找路径和预览文件。

## <a name="additional-features"></a>其他功能

针对 VS Code 的 Data Lake 工具支持以下功能：

-   IntelliSense 自动完成：在弹出窗口中围绕关键字、方法和变量等项显示建议。 不同图标表示不同类型的对象：

    - Scala 数据类型
    - 复杂数据类型
    - 内置 UDT
    - .NET 集合与类
    - C# 表达式
    - 内置 C# UDF、UDO 和 UDAAG 
    - U-SQL 函数
    - U-SQL 开窗函数
 
    ![用于 Visual Studio Code 的 Data Lake 工具 IntelliSense 对象类型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   IntelliSense 自动完成 Data Lake Analytics 元数据：Data Lake 工具在本地下载 Data Lake Analytics 元数据信息。 IntelliSense 功能自动填充 Data Lake Analytics 元数据中的对象，包括数据库、架构、表、视图、表值函数、过程和 C# 程序集。
 
    ![用于 Visual Studio Code 的 Data Lake 工具 IntelliSense 元数据](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense 错误标记：Data Lake 工具使用下划线标出 U-SQL 和 C# 编辑错误。 
-   语法突出显示：Data Lake 工具使用不同颜色来区分变量、关键字、数据类型和函数等项。 

    ![用于 Visual Studio Code 的 Data Lake 工具语法突出显示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

   > [!NOTE]
   > Microsoft 建议你升级到针对 Visual Studio 的 Azure Data Lake 工具 2.3.3000.4 或更高版本。 以前的版本不再可以下载，现已弃用。  
   
## <a name="next-steps"></a>后续步骤
- [在 VS Code 中使用 Python、R 和 CSharp 开发用于 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)
- [教程：Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
- [教程：使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
