---
title: "Azure Data Lake 工具 - 使用用于 Visual Studio Code 的 Azure Data Lake 工具 | Microsoft Docs"
description: "了解如何使用用于 Visual Studio Code 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。 "
Keywords: "VScode, Azure Data Lake 工具, 本地运行, 本地调试, 预览存储文件, 上传到存储路径"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: zh-cn
ms.lasthandoff: 07/17/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>使用用于 Visual Studio Code 的 Azure Data Lake 工具

了解如何使用用于 Visual Studio Code (VSCode) 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。  这些信息也包含在以下视频中：

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>先决条件

可在 VSCode 支持的平台（包括 Windows、Linux 和 MacOS）上安装 Data Lake 工具。 可查找用于不同平台的必备组件

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Java SE Runtime Environment 版本 8 更新 77 或更高版本](https://java.com/download/manual.jsp)。 需要将 java.exe 路径添加到系统环境变量路径。  有关说明，请参阅[如何设置或更改 Path 系统变量？]( https://www.java.com/download/help/path.xml) 该路径类似于 C:\Program Files\Java\jdk1.8.0_77\jre\bin
    - [.NET Core SDK 1.0.3 或 .NET Core 1.1 运行时](https://www.microsoft.com/net/download)。
    
- Linux（建议 Ubuntu 14.04 LTS）

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。 使用以下命令安装：

        sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/)。 

        - 执行以下命令更新 deb 包源：

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - 运行以下命令安装 mono：

                sudo apt-get install mono-complete

            > [!NOTE] 
            > 不支持 Mono 4.6。  安装 4.2.x 版之前，需要完全卸载 4.6 版。  

        - [Java SE Runtime Environment 版本 8 更新 77 或更高版本](https://java.com/download/manual.jsp)。 [此处]( https://java.com/en/download/help/linux_x64_install.xml)提供了相关说明。
        - [.NET Core SDK 1.0.3 或 .NET Core 1.1 运行时](https://www.microsoft.com/net/download)。
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment 版本 8 更新 77 或更高版本](https://java.com/download/manual.jsp)。 [此处](https://java.com/en/download/help/mac_install.xml)提供了相关说明。
    - [.NET Core SDK 1.0.3 或 .NET Core 1.1 运行时](https://www.microsoft.com/net/download)。

## <a name="install-the-data-lake-tools"></a>安装 Data Lake 工具

安装必备组件后，即可安装用于 VSCode 的 Data Lake 工具。

**安装 Data Lake 工具的方法**

1. 打开 **Visual Studio Code**。
2. 按 **Ctrl+P**，然后输入：
```
ext install usql-vscode-ext
```
可看到 Visual Studio Code 扩展的列表。 其中一个是 **Azure Data Lake 工具**。

3. 单击“Azure Data Lake 工具”旁的“安装”。 数秒后，“安装”按钮将变为“重载”。
4. 单击“重载”激活扩展。
5. 单击“确定”以确认。 在“扩展”窗格中，可看到 Azure Data Lake 工具。
    ![安装用于 Visual Studio Code 的 Data Lake 工具](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>激活 Azure Data Lake 工具
请创建新的 .USQL 文件，或打开现有 .USQL 文件以激活扩展。 

## <a name="connect-to-azure"></a>连接到 Azure

必须首先接到 Azure 帐户，才能在 Azure Data Lake Analytics 中编译和运行 U-SQL 脚本。

**连接到 Azure 的方法**

1.  按 **CTRL+SHIFT+P** 打开命令面板。 
2.  输入 **ADL: Login**。 登录信息会显示在输出窗格中。

    ![用于 Visual Studio Code 的 Data Lake 工具命令面板](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![用于 Visual Studio Code 的 Data Lake 工具登录信息](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. 按住 Ctrl 键并单击登录 URL：https://aka.ms/devicelogin 以打开登录网页。 复制代码 G567LX42V 并粘贴到下面的文本框中，单击“继续”以继续。

   ![用于 Visual Studio Code 的 Data Lake 工具登录粘贴代码](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  按照说明从网页登录。 连接后，VSCode 窗口左下角的状态栏上会显示 Azure 帐户名称。 

    > [!NOTE] 
    > 如果帐户已启用双因素身份验证，建议使用手机身份验证，而不使用 Pin 身份验证。

若要注销，请使用命令 **ADL: Logout**

## <a name="list-data-lake-analytics-accounts"></a>列出 Data Lake Analytics 帐户

若要测试连接，可列出 Data Lake Analytics 帐户：

**列出 Azure 订阅下 Data Lake Analytics 帐户的方法**

1. 按 **CTRL+SHIFT+P** 打开命令面板。
2. 输入 **ADL: List Accounts**。  帐户随即显示在“输出”窗格中。

## <a name="open-sample-script"></a>打开示例脚本

使用命令面板 (**Ctrl+Shift+P**)，然后选择“ADL: Open Sample Script”。 然后它会打开此示例的另一个实例。 还可以对此实例编辑、配置、提交脚本。

## <a name="work-with-u-sql"></a>处理 U-SQL

若要处理 U-SQL，需打开 U-SQL 文件或文件夹。

**打开 U-SQL 项目文件夹的方法**

1. 从 Visual Studio Code 中，单击“文件”菜单，然后单击“打开文件夹”。
2. 指定文件夹，然后单击“选择文件夹”。
3. 依次单击“文件”菜单、“新建”。 **Untilted-1** 便已添加到项目。
4. 将以下代码复制并粘贴到 Untitled-1 文件中：

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
            TO “/Output/departments.csv”

    脚本创建具有 /output 文件夹中某些数据的 departments.csv 文件。

5. 在打开的文件夹中，将文件命名为 **myUSQL.usql**，并保存。 请注意，**adltools_settings.json** 配置文件也添加到了此项目。
4. 打开 **adltools_settings.json**，并对其配置以下属性：

    - 帐户：Azure 订阅下的 Data Lake Analytics 帐户。
    - 数据库：你帐户下的数据库。 默认为 master。
    - 架构：你数据库下的架构。 默认为 dbo。
    - 可选设置：
        - 优先级：优先级范围是 1 到 1000，1 是最高优先级。 默认值为 1000。
        - 并行度：并行度范围是 1 到 150。 默认值是 ADLA 帐户中允许的最大并行度。 
        
        > [!NOTE] 
        > 如果设置无效，则使用默认值。

    ![用于 Visual Studio Code 的 Data Lake 工具配置文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    需要使用 Data Lake Analytics 计算机帐户编译和运行 U-SQL 作业。  必须先配置此计算机帐户，然后才能编译和运行 U-SQL 作业。
    
    配置保存之后，帐户 | 数据库 | 架构信息会显示在对应 USQL 文件左下角的状态栏上。 
 
 

与打开文件相比，通过打开文件夹可以：

- 使用代码隐藏文件。  在单文件模式下，不支持代码隐藏。
- 使用配置文件。 打开文件夹后，工作文件夹中的脚本共享同一配置文件。


Data Lake Analytics 服务远程执行 U-SQL 脚本编译。  发出编译命令时，U-SQL 脚本便会发送到 Data Lake Analytics 帐户。 Visual Studio Code 在后期会收到编译结果。 由于进行远程编译，Visual Studio Code 需要将信息连接到配置文件中的 Data Lake Analytics 帐户。

**编译 U-SQL 脚本的方法**

1. 按 **CTRL+SHIFT+P** 打开命令面板。 
2. 输入“ADL: Compile Script”。 编译结果显示在输出窗口中。 也可以右键单击脚本文件，然后单击“ADL: Compile Script”编译 U-SQL 作业。 编译结果显示在输出窗格中。
 

**提交 U-SQL 脚本的方法**

1. 按 **CTRL+SHIFT+P** 打开命令面板。 
2. 输入“ADL: Submit Job”。  也可以右键单击脚本文件，然后单击“ADL: Submit Job”提交 U-SQL 作业。 

提交 U-SQL 作业后，将在 VSCode 的输出窗口中显示提交日志。 如果提交成功，还会显示作业 URL。 可在 Web 浏览器中打开该作业 URL，跟踪作业实时状态。

若要启用输出作业详细信息：请在 **vscode for u-sql_settings.json** 文件中设置“jobInformationOutputPath”。
 
## <a name="use-code-behind-file"></a>使用代码隐藏文件

代码隐藏文件是与一个 U-SQL 脚本关联的 CSharp 文件。 可在代码隐藏文件中定义脚本专用 UDO/UDA/UDT/UDF。 无需提前注册程序集，即可直接在脚本中使用 UDO/UDA/UDT/UDF。 代码隐藏文件放置在其对等 U-SQL 脚本文件所在的文件夹中。 如果脚本名为 xxx.usql，则代码隐藏文件命名为 xxx.usql.cs。 手动删除代码隐藏文件会对关联的 U-SQL 脚本禁用代码隐藏功能。 有关为 U-SQL 脚本编写自定义代码的详细信息，请参阅 [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)（在 U-SQL 中编写和使用自定义代码 - 用户定义函数）。

若要支持代码隐藏，必须打开工作文件夹。 

**生成代码隐藏文件的方法**

1. 打开源文件。 
2. 按 **CTRL+SHIFT+P** 打开命令面板。
3. 输入“ADL: Generate Code Behind”。  在同一文件夹中创建了代码隐藏文件。 

也可以右键单击脚本文件，然后单击“ADL: Generate Code Behind”以生成代码隐藏文件。 

采用类似于独立 U-SQL 脚本的方式，编译和提交含隐藏代码的 U-SQL 脚本。

以下两个屏幕截图显示了代码隐藏文件及其关联的 U-SQL 脚本文件：
 
![用于 Visual Studio Code 的 Data Lake 工具代码隐藏](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![用于 Visual Studio Code 的 Data Lake 工具代码隐藏](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>使用程序集

有关开发程序集的信息，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集](data-lake-analytics-u-sql-develop-assemblies.md)。

使用 Data Lake 工具，可将自定义代码程序集注册到 Data Lake Analytics 目录。

**注册程序集的方法**

可以通过 **ADL: Register Assembly** 或**ADL: Register Assembly through Configuration** 注册程序集。

**ADL: Register Assembly**
1.  按 **CTRL+SHIFT+P** 打开命令面板。
2.  输入 **ADL: Register Assembly**。 
3.  指定本地程序集路径。 
4.  选择 Data Lake Analytics 帐户。
5.  选择数据库。

结果：门户在浏览器中打开并显示程序集注册过程。  

触发 **ADL: Register Assembly** 命令的另一种方便方法是在资源管理器中右键单击 dll 文件。 

**ADL: Register Assembly through Configuration**。
1.  按 **CTRL+SHIFT+P** 打开命令面板。
2.  输入 **ADL: Register Assembly through Configuration**。 
3.  指定本地程序集路径。 
4.  会显示 json 文件。 检查并根据需要编辑程序集依赖项和资源参数。 指令会显示在输出窗口中。 保存 (**CTRL+S**) Json 文件以继续进行程序集注册。

![用于 Visual Studio Code 的 Data Lake 工具代码隐藏](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- 程序集依赖项：Azure Data Lake 工具会自动检测 DLL 是否具有任何依赖项。 依赖项在检测到之后会显示在 Json 文件中。 
>- 资源：可以在程序集注册过程中上传 DLL 资源（例如，txt、png、csv 等）。 

触发 **ADL: Register Assembly through Configuration** 命令的另一种更方便方法是在资源管理器中右键单击 dll 文件。 

以下 U-SQL 代码演示如何调用程序集。 在示例中，程序集名称是 test。

```
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
```


## <a name="access-data-lake-analytics-catalog"></a>访问 Data Lake Analytics 目录

连接到 Azure 后，可使用以下步骤访问 U-SQL 目录：

**访问 Azure Data Lake Analytics 元数据**

1.  按 **CTRL+SHIFT+P**，然后输入 **ADL: List Tables**。
2.  单击其中一个 Data Lake Analytics 帐户。
3.  单击其中一个 Data Lake Analytics 数据库。
4.  单击其中一个架构。 随即可看到表。

## <a name="show-data-lake-analytics-jobs"></a>显示 Data Lake Analytics 作业

使用命令面板 (**Ctrl+Shift+P**)，然后选择“ADL: Show Job”。 

1.  选择 ADLA 或本地帐户 
2.  等待显示帐户的作业列表
3.  从作业列表中选择作业，ADL 工具会在门户中打开作业详细信息，并在 VSCode 中显示 JobInfo 文件。

![用于 Visual Studio Code 的 Data Lake 工具 IntelliSense 对象类型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Azure Data Lake 存储 (ADLS) 集成

可以直接在 VSCode 中使用 ADLS 相关命令导航 ADLS 资源、预览 ADLS 文件并将文件上传到 ADLS 中。  
### <a name="list-storage-path"></a>列出存储路径

使用命令面板 (**Ctrl+Shift+P**)，然后选择“ADL: List Storage Path”。
1.  打开命令面板并输入命令。

    ![用于 Visual Studio Code 的 Data Lake 工具列出存储路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  选择用于列出存储路径的首选方法。 这段使用“输入路径”作为示例。

    ![用于 Visual Studio Code 的 Data Lake 工具列出存储路径（供选择一种方式）](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- Vscode 会保留每次访问上一个 ADLA 帐户的路径。 示例：/tt/ss。
    >- 从根路径浏览：列出根路径，从中选择 ADLA 帐户或本地帐户。
    >- 输入路径：列出指定路径，从中选择 ADLA 帐户或本地帐户。
    
3. 从本地或 ADLA 帐户选择帐户。

    ![用于 Visual Studio Code 的 Data Lake 工具选择“更多”](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  单击“更多”可列出更多 ADLA 帐户并选择 ADLA 帐户。

    ![用于 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  输入 Azure 存储帐户。 例如：/output

       ![用于 Visual Studio Code 的 Data Lake 工具输入存储路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  结果：命令面板基于输入列出路径信息。

    ![用于 Visual Studio Code 的 Data Lake 工具列出存储路径结果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

列出相对路径的另一种更方便方法是通过右键单击上下文菜单。

1.  右键单击路径字符串以选择“List Storage Path”。

       ![用于 Visual Studio Code 的 Data Lake 工具右键单击上下文菜单](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. 所选相对路径会显示在命令面板中。

   ![用于 Visual Studio Code 的 Data Lake 工具列出所选路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  从本地或 ADLA 帐户选择帐户。

       ![用于 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  结果：命令面板列出当前路径的文件夹和文件。

       ![用于 Visual Studio Code 的 Data Lake 工具列出当前路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>预览存储文件

使用命令面板 (**Ctrl+Shift+P**)，然后选择“ADL: : Preview Storage File”。
1.  打开命令面板并输入命令。

       ![用于 Visual Studio Code 的 Data Lake 工具预览存储文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  从本地或 ADLA 选择帐户。

       ![用于 Visual Studio Code 的 Data Lake 工具列出帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  单击“更多”可列出更多 ADLA 帐户并选择 ADLA 帐户。

       ![用于 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  输入 Azure 存储帐户或文件。 例如：/output/SearchLog.txt

       ![用于 Visual Studio Code 的 Data Lake 工具输入存储路径和文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  结果：命令面板基于输入列出路径信息。

       ![用于 Visual Studio Code 的 Data Lake 工具预览文件结果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

预览文件的另一种更方便方法是通过右键单击上下文菜单。

1.  右键单击文件路径以预览文件。

       ![用于 Visual Studio Code 的 Data Lake 工具右键单击上下文菜单](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  从本地或 ADLA 帐户选择帐户。

       ![用于 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  结果：VSCode 显示文件的预览结果。

       ![用于 Visual Studio Code 的 Data Lake 工具预览文件结果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>上传文件 

可以通过命令 **ADL: Upload File** 或 **ADL: Upload File through Configuration** 上传文件。

**ADL: Upload File**
1.  按 CTRL+SHIFT+P 以打开命令面板，或在脚本编辑器中右键单击，然后输入 **Upload File**。
2. 输入用于上传的本地路径。

    ![用于 Visual Studio Code 的 Data Lake 工具输入本地路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. 选择一种方法来列出存储路径。 这段使用“输入路径”作为示例。

    ![用于 Visual Studio Code 的 Data Lake 工具列出存储路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- Vscode 会保留每次访问上一个 ADLA 帐户的路径。 示例：/tt/ss。
    >- 从根路径浏览：列出根路径，从中选择 ADLA 帐户或本地帐户。
    >- 输入路径：列出指定路径，从中选择 ADLA 帐户或本地帐户。

4. 从本地或 ADLA 帐户选择帐户。

    ![用于 Visual Studio Code 的 Data Lake 工具右键单击存储](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  输入 Azure 存储帐户。 例如：/output/

       ![用于 Visual Studio Code 的 Data Lake 工具输入存储路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. 列出输入的 Azure 存储路径。 选择“选择当前文件夹”。

    ![用于 Visual Studio Code 的 Data Lake 工具选择文件夹](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  结果：输出窗口显示文件上传状态。

       ![用于 Visual Studio Code 的 Data Lake 工具上传状态](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration**
1.  按 CTRL+SHIFT+P 以打开命令面板，或在脚本编辑器中右键单击，然后输入 **Upload File through Configuration**。
2.  VSCode 显示 json 文件。 可以输入文件路径，并同时上传多个文件。 指令会显示在输出窗口中。 保存 (**CTRL + S**) Json 文件以继续进行文件上传。

       ![用于 Visual Studio Code 的 Data Lake 工具输入文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  结果：输出窗口显示文件上传状态。

       ![用于 Visual Studio Code 的 Data Lake 工具上传状态](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

将文件上传到存储的另一种方便方法是在脚本编辑器中通过文件完整路径或文件相对路径上的右键单击菜单。 输入本地文件路径并选择帐户，然后输出窗口会显示正在上传状态。 

### <a name="open-web-azure-storage-explorer"></a>打开 Web Azure 存储资源管理器
可以通过命令 **ADL: Open Web Azure Storage Explorer** 或从右键单击上下文菜单打开 **Web Azure 存储资源管理器**。

1. 按 CTRL+SHIFT+P 打开命令面板。
2. 输入 **Open Web Azure Storage Explorer**，或在脚本编辑器中的相对路径或完整路径上右键单击，以选择“Open Web Azure Storage Explorer”。
3. 选择 Data Lake Analytics 帐户。

ADL 工具会在门户中打开 Azure 存储路径。 可以从 Web 访问路径和预览文件。

### <a name="local-run-and-local-debug-windows-users"></a>本地运行和本地调试 Windows 用户
实现了 U-SQL 本地运行，以便在将代码发布到 ADLA 之前测试本地数据以及在本地验证脚本。 本地调试功能使你可以在提交到 ADLA 之前调试 C# 代码隐藏、逐步执行代码、在本地验证脚本。 请参阅说明：[使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)。

## <a name="additional-features"></a>其他功能

用于 VSCode 的 Data Lake 工具支持以下功能：

-   IntelliSense 自动完成。 弹出关键字、方法、变量等相关建议。不同图标表示不同类型的对象：

    - Scala 数据类型
    - 复杂数据类型
    - 内置 UDT
    - .Net 集合和类
    - C# 表达式
    - 内置 C# UDF、UDO 和 UDAAG 
    - U-SQL 函数
    - U-SQL 开窗函数
 
    ![用于 Visual Studio Code 的 Data Lake 工具 IntelliSense 对象类型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   IntelliSense 自动完成 Data Lake Analytics 元数据。 Data Lake 工具在本地下载 Data Lake Analytics 元数据信息。  IntelliSense 功能自动填充 Data Lake Analytics 元数据中的对象，包括数据库架构、表、视图、TVF、过程、C# 程序集。
 
    ![用于 Visual Studio Code 的 Data Lake 工具 IntelliSense 元数据](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense 错误标记。 Data Lake 工具使用下划线标出 U-SQL 和 C# 编辑错误。 
-   语法突出显示。 Data Lake 工具使用不同颜色来区分变量、关键字、数据类型、函数等。 

    ![用于 Visual Studio Code 的 Data Lake 工具语法突出显示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>后续步骤

- 对于使用 Visual Studio Code 进行的 U-SQL 本地运行和本地调试，请参阅[使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)。
- 有关 Data Lake Analytics 的入门信息，请参阅[教程：Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。
- 有关如何使用用于 Visual Studio 的 Data Lake 工具的信息，请参阅[教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
- 有关开发程序集的信息，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集](data-lake-analytics-u-sql-develop-assemblies.md)。




