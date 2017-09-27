---
title: "Azure Data Lake 工具：使用针对 Visual Studio Code 的 Azure Data Lake 工具 | Microsoft Docs"
description: "了解如何使用针对 Visual Studio Code 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。 "
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
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 833d14af47454a01fa3c97ffa854d688dd35871f
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---

# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>使用针对 Visual Studio Code 的 Azure Data Lake 工具

了解如何使用针对 Visual Studio Code (VS Code) 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。 这些信息也包含在以下视频中：

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>先决条件

可在 VS Code 支持的平台上安装 Data Lake 工具。 支持的平台包括 Windows、Linux 和 MacOS。 不同平台的先决条件如下：

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Java SE Runtime Environment 版本 8 更新 77 或更高版本](https://java.com/download/manual.jsp)。 将 java.exe 路径添加到系统环境变量路径。 有关配置说明，请参阅[如何设置或更改 Path 系统变量？]( https://www.java.com/download/help/path.xml) 该路径类似于 C:\Program Files\Java\jdk1.8.0_77\jre\bin。
    - [.NET Core SDK 1.0.3 或 .NET Core 1.1 运行时](https://www.microsoft.com/net/download)。
    
- Linux（建议 Ubuntu 14.04 LTS）

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。 若要安装 Code，请输入以下命令：

              sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/)。 

        - 若要更新 deb 包源，请输入以下命令：

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - 若要安装 Mono，请输入以下命令：

                sudo apt-get install mono-complete

            > [!NOTE] 
            > 不支持 Mono 4.6。 安装 4.2.x 之前，请完全卸载版本 4.6。  

        - [Java SE Runtime Environment 版本 8 更新 77 或更高版本](https://java.com/download/manual.jsp)。 有关安装说明，请参阅[适用于 Java 的 Linux 64 位安装说明]( https://java.com/en/download/help/linux_x64_install.xml)页。
        - [.NET Core SDK 1.0.3 或 .NET Core 1.1 运行时](https://www.microsoft.com/net/download)。
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment 版本 8 更新 77 或更高版本](https://java.com/download/manual.jsp)。 有关安装说明，请参阅[适用于 Java 的 Linux 64 位安装说明](https://java.com/en/download/help/mac_install.xml)页。
    - [.NET Core SDK 1.0.3 或 .NET Core 1.1 运行时](https://www.microsoft.com/net/download)。

## <a name="install-data-lake-tools"></a>安装 Data Lake 工具

安装必备组件后，即可安装用于 VS Code 的 Data Lake 工具。

**安装 Data Lake 工具**

1. 打开 Visual Studio Code。
2. 按 Ctrl+P，并输入以下命令：
```
ext install usql-vscode-ext
```
可看到 Visual Studio Code 扩展的列表。 其中一个是 **Azure Data Lake 工具**。

3. 选择“Azure Data Lake 工具”旁的“安装”。 数秒后，“安装”按钮会变为“重载”。
4. 选择“重载”激活扩展。
5. 选择“确定”以确认。 在“扩展”窗格中，可看到“Azure Data Lake 工具”。
    ![针对 Visual Studio Code 的 Data Lake 工具扩展窗格](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>激活 Azure Data Lake 工具
创建新的 .usql 文件，或打开现有的 .usql 文件以激活扩展。 

## <a name="connect-to-azure"></a>连接到 Azure

必须首先接到 Azure 帐户，才能在 Data Lake Analytics 中编译和运行 U-SQL 脚本。

**连接到 Azure 的方法**

1.  按 Ctrl+Shift+P 打开命令面板。 
2.  输入 **ADL: Login**。 登录信息显示在“输出”窗格中。

    ![针对 Visual Studio Code 的 Data Lake 工具命令面板](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![针对 Visual Studio Code 设备的 Data Lake 工具登录信息](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. 按住 Ctrl 键并单击登录 URL：https://aka.ms/devicelogin 以打开登录网页。 在文本框中输入代码 **G567LX42V** 并选择“继续”。

   ![针对 Visual Studio Code 的 Data Lake 工具登录粘贴代码](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  按照说明从网页登录。 连接后，“VS Code”窗口左下角的状态栏上会显示 Azure 帐户名称。 

    > [!NOTE] 
    > 如果帐户已启用双重身份验证，我们建议使用手机身份验证，而不要使用 PIN 码。

若要注销，请输入命令 **ADL: Logout**

## <a name="list-your-data-lake-analytics-accounts"></a>列出 Data Lake Analytics 帐户

若要测试连接，请获取 Data Lake Analytics 帐户的列表。

**列出 Azure 订阅下 Data Lake Analytics 帐户的方法**

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 **ADL: List Accounts**。 帐户随即显示在“输出”窗格中。

## <a name="open-the-sample-script"></a>打开示例脚本
打开命令面板 (Ctrl+Shift+P)，并输入 **ADL: Open Sample Script**。 这会打开此示例的另一个实例。 还可以对此实例编辑、配置和提交脚本。

## <a name="work-with-u-sql"></a>处理 U-SQL

若要处理 U-SQL，需打开 U-SQL 文件或文件夹。

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

5. 在打开的文件夹中，将文件命名为 **myUSQL.usql**，并保存。 adltools_settings.json 配置文件也添加到了此项目。
4. 打开 adltools_settings.json，并对其配置以下属性：

    - 帐户：Azure 订阅下的 Data Lake Analytics 帐户。
    - 数据库：你帐户下的数据库。 默认为 **master**。
    - 架构：你数据库下的架构。 默认为 **dbo**。
    - 可选设置：
        - 优先级：优先级范围是 1 到 1000，1 是最高优先级。 默认值为 **1000**。
        - 并行度：并行度范围是 1 到 150。 默认值是 Azure Data Lake Analytics 帐户中允许的最大并行度。 
        
        > [!NOTE] 
        > 如果设置无效，则使用默认值。

    ![针对 Visual Studio Code 的 Data Lake 工具配置文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    需要使用 Data Lake Analytics 计算帐户编译并运行 U-SQL 作业。 必须先配置此计算机帐户，才能编译和运行 U-SQL 作业。
    
保存配置后，帐户、数据库和架构信息会显示在对应 .usql 文件左下角的状态栏上。 
 
 
与打开文件相比，打开文件夹可以：

- 使用代码隐藏文件。 在单文件模式下，不支持代码隐藏。
- 添加配置文件。 打开文件夹时，工作文件夹中的脚本共享单个配置文件。


U-SQL 脚本通过 Data Lake Analytics 服务远程进行编译。 发出 **compile** 命令时，U-SQL 脚本便会发送到 Data Lake Analytics 帐户。 随后，Visual Studio Code 会收到编译结果。 由于进行远程编译，Visual Studio Code 需要列出信息才能连接到配置文件中的 Data Lake Analytics 帐户。

**编译 U-SQL 脚本的方法**

1. 按 Ctrl+Shift+P 打开命令面板。 
2. 输入“ADL: Compile Script”。 编译结果显示在“输出”窗口中。 也可以右键单击脚本文件，并选择“ADL: Compile Script”编译 U-SQL 作业。 编译结果显示在“输出”窗格中。
 

**提交 U-SQL 脚本的方法**

1. 按 Ctrl+Shift+P 打开命令面板。 
2. 输入“ADL: Submit Job”。  也可以右键单击脚本文件，并选择“ADL: Submit Job”。 

提交 U-SQL 作业后，提交日志会显示在 VS Code 的“输出”窗口中。 如果提交成功，还会显示作业 URL。 可在 Web 浏览器中打开该作业 URL，跟踪作业实时状态。

若要启用输出作业详细信息，请在 **vscode for u-sql_settings.json** 文件中设置 **jobInformationOutputPath**。
 
## <a name="use-a-code-behind-file"></a>使用代码隐藏文件

代码隐藏文件是与单个 U-SQL 脚本关联的 C# 文件。 可在代码隐藏文件中定义 UDO、UDA、UDT 和 UDF 专用的脚本。 无需事先注册程序集，即可直接在脚本中使用 UDO、UDA、UDT 和 UDF。 代码隐藏文件放置在其对等 U-SQL 脚本文件所在的文件夹中。 如果脚本名为 xxx.usql，则代码隐藏文件命名为 xxx.usql.cs。 如果手动删除代码隐藏文件，会对关联的 U-SQL 脚本禁用代码隐藏功能。 有关为 U-SQL 脚本编写自定义代码的详细信息，请参阅[在 U-SQL 中编写和使用自定义代码：用户定义的函数]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)。

若要支持代码隐藏，必须打开工作文件夹。 

**生成代码隐藏文件的方法**

1. 打开源文件。 
2. 按 Ctrl+Shift+P 打开命令面板。
3. 输入“ADL: Generate Code Behind”。 在同一文件夹中创建了代码隐藏文件。 

也可以右键单击脚本文件，并选择“ADL: Generate Code Behind”。 

使用隐藏代码文件编译和提交 U-SQL 脚本的方式类似于使用独立的 U-SQL 脚本文件。

以下两个屏幕截图显示了代码隐藏文件及其关联的 U-SQL 脚本文件：
 
![针对 Visual Studio Code 的 Data Lake 工具代码隐藏](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![针对 Visual Studio Code 的 Data Lake 工具代码隐藏脚本文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>使用程序集

有关开发程序集的信息，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集](data-lake-analytics-u-sql-develop-assemblies.md)。

可以使用 Data Lake 工具在 Data Lake Analytics 目录中注册自定义代码程序集。

**注册程序集的方法**

可以通过 **ADL: Register Assembly** 或 **ADL: Register Assembly through Configuration** 命令注册程序集。

**通过 ADL: Register Assembly 命令注册**
1.  按 Ctrl+Shift+P 打开命令面板。
2.  输入 **ADL: Register Assembly**。 
3.  指定本地程序集路径。 
4.  选择 Data Lake Analytics 帐户。
5.  选择数据库。

结果：门户在浏览器中打开并显示程序集注册过程。  

触发 **ADL: Register Assembly** 命令的另一种便利方法是在文件资源管理器中右键单击 .dll 文件。 

**通过 ADL: Register Assembly through Configuration 命令注册**
1.  按 Ctrl+Shift+P 打开命令面板。
2.  输入 **ADL: Register Assembly through Configuration**。 
3.  指定本地程序集路径。 
4.  会显示 JSON 文件。 检查并根据需要编辑程序集依赖项和资源参数。 指令会显示在“输出”窗口中。 若要继续进行程序集注册，请保存 (Ctrl+S) JSON 文件。

![针对 Visual Studio Code 的 Data Lake 工具代码隐藏](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- 程序集依赖项：Azure Data Lake 工具会自动检测 DLL 是否具有任何依赖项。 依赖项在检测到之后会显示在 JSON 文件中。 
>- 资源：可以在程序集注册过程中上传 DLL 资源（例如，.txt、.png 和 .csv）。 

触发 **ADL: Register Assembly through Configuration** 命令的另一种便利方法是在文件资源管理器中右键单击 .dll 文件。 

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


## <a name="access-the-data-lake-analytics-catalog"></a>访问 Data Lake Analytics 目录

连接到 Azure 后，可使用以下步骤访问 U-SQL 目录：

**访问 Azure Data Lake Analytics 元数据**

1.  按 Ctrl+Shift+P，并输入 **ADL: List Tables**。
2.  选择其中一个 Data Lake Analytics 帐户。
3.  选择其中一个 Data Lake Analytics 数据库。
4.  选择其中一个架构。 可以看到表的列表。

## <a name="view-data-lake-analytics-jobs"></a>查看 Data Lake Analytics 作业

**查看 Data Lake Analytics 作业**
1.  打开命令面板 (Ctrl+Shift+P)，并选择“ADL: Show Job”。 
2.  选择 Data Lake Analytics 或本地帐户。 
3.  等待显示帐户的作业列表
4.  从作业列表中选择作业，Data Lake 工具会在 Azure 门户中打开作业详细信息，并在 VS Code 中显示 JobInfo 文件。

![针对 Visual Studio Code 的 Data Lake 工具 IntelliSense 对象类型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Azure Data Lake Storage 集成

可以使用 Azure Data Lake Storage 相关的命令来执行以下操作：
 - 浏览 Azure Data Lake Storage 资源。 
 - 预览 Azure Data Lake Storage 文件。  
 - 在 VS Code 中将文件上传到 Azure Data Lake Storage。 

### <a name="list-the-storage-path"></a>列出存储路径 
可以通过命令面板或右键单击来列出存储路径。

**通过命令面板列出存储路径**

1.  打开命令面板 (Ctrl+Shift+P)，并输入 **ADL: List Storage Path**。

    ![针对 Visual Studio Code 的 Data Lake 工具列出存储路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  选择用于列出存储路径的首选方法。 这段使用“输入路径”作为示例。

    ![在针对 Visual Studio Code 的 Data Lake 工具中列出存储路径的方法之一](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- VS Code 会保留每个 Data Lake Analytics 帐户中上次访问的路径。 例如：/tt/ss。
    >- 从根路径浏览：所选 Data Lake Analytics 帐户中的列表根路径，或本地路径。
    >- 输入路径：列出所选 Data Lake Analytics 帐户中的指定路径，或列出本地路径。
    
3. 选择本地路径中的某个帐户，或选择一个 Data Lake Analytics 帐户。

    ![针对 Visual Studio Code 的 Data Lake 工具选择“更多”](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4. 选择“更多”可列出更多 Data Lake Analytics 帐户并选择 Data Lake Analytics 帐户。

    ![针对 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  输入 Azure 存储路径。 例如 /output。

    ![针对 Visual Studio Code 的 Data Lake 工具输入存储路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  结果：命令面板基于输入内容列出路径信息。

    ![针对 Visual Studio Code 的 Data Lake 工具列出存储路径结果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

列出相对路径的一种更便利方法是右键单击上下文菜单。

**通过右键单击列出存储路径**

1.  右键单击路径字符串以选择“List Storage Path”。

       ![针对 Visual Studio Code 的 Data Lake 工具右键单击上下文菜单](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

2. 所选相对路径会显示在命令面板中。

   ![针对 Visual Studio Code 的 Data Lake 工具：所选相对路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  选择本地路径中的某个帐户，或选择一个 Data Lake Analytics 帐户。

       ![针对 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  结果：命令面板列出当前路径的文件夹和文件。

       ![针对 Visual Studio Code 的 Data Lake 工具：从当前路径列出](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-the-storage-file"></a>预览存储文件
可以通过命令面板或右键单击来预览存储文件。

**通过命令面板预览存储文件**

1.  打开命令面板 (Ctrl+Shift+P)，并输入 **ADL: Preview Storage File**。

       ![针对 Visual Studio Code 的 Data Lake 工具预览存储文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  选择本地路径中的某个帐户，或选择一个 Data Lake Analytics 帐户。

       ![针对 Visual Studio Code 的 Data Lake 工具列出帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  选择“更多”可列出更多 Data Lake Analytics 帐户并选择 Data Lake Analytics 帐户。

       ![针对 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  输入 Azure 存储路径或文件。 例如 /output/SearchLog.txt。

       ![针对 Visual Studio Code 的 Data Lake 工具输入存储路径和文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  结果：命令面板基于输入内容列出路径信息。

       ![针对 Visual Studio Code 的 Data Lake 工具预览文件结果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

**通过右键单击列出存储路径**

1.  若要预览文件，请右键单击文件路径。

   ![针对 Visual Studio Code 的 Data Lake 工具右键单击上下文菜单](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png) 

2.  选择本地路径中的某个帐户，或选择一个 Data Lake Analytics 帐户。

       ![针对 Visual Studio Code 的 Data Lake 工具选择帐户](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  结果：VS Code 显示文件的预览结果。

       ![针对 Visual Studio Code 的 Data Lake 工具预览文件结果](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-a-file"></a>上传文件 

可以通过输入命令 **ADL: Upload File** 或 **ADL: Upload File through Configuration** 上传文件。

**通过 ADL: Upload File 命令上传文件**
1. 按 Ctrl+Shift+P 打开命令面板，或右键单击脚本编辑器并输入 **Upload File**。
2.  若要上传文件，请输入本地路径。

    ![针对 Visual Studio Code 的 Data Lake 工具输入本地路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. 选择一种列出存储路径的方法。 这段使用“输入路径”作为示例。

    ![针对 Visual Studio Code 的 Data Lake 工具列出存储路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- VS Code 会保留每个 Data Lake Analytics 帐户中上次访问的路径。 例如：/tt/ss。
    >- 从根路径浏览：所选 Data Lake Analytics 帐户中的列表根路径，或本地路径。
    >- 输入路径：列出所选 Data Lake Analytics 帐户中的指定路径，或列出本地路径。

4. 选择本地路径中的某个帐户，或选择一个 Data Lake Analytics 帐户。

    ![针对 Visual Studio Code 的 Data Lake 工具右键单击存储](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5. 输入 Azure 存储路径。 例如：/output。

       ![Data Lake Tools for Visual Studio Code enter storage path](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. 查找 Azure 存储路径。 选择“选择当前文件夹”。

    ![针对 Visual Studio Code 的 Data Lake 工具选择文件夹](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  结果：“输出”窗口显示文件上传状态。

       ![针对 Visual Studio Code 的 Data Lake 工具上传状态](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**通过 ADL: Upload File through Configuration 命令上传文件**
1.  按 Ctrl+Shift+P 打开命令面板，或右键单击脚本编辑器并输入 **Upload File through Configuration**。
2.  VS Code 显示 JSON 文件。 可以输入文件路径，并同时上传多个文件。 指令会显示在“输出”窗口中。 若要继续上传文件，请保存 (Ctrl+S) JSON 文件。

       ![针对 Visual Studio Code 的 Data Lake 工具文件路径](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  结果：“输出”窗口显示文件上传状态。

       ![针对 Visual Studio Code 的 Data Lake 工具上传状态](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

将文件上传到存储的另一种方法是在脚本编辑器中通过文件完整路径或文件相对路径上的右键单击菜单。 输入本地文件路径并选择帐户。 “输出”窗口显示上传状态。 

### <a name="open-azure-storage-explorer"></a>打开 Azure 存储资源管理器
可以通过输入命令 **ADL: Open Web Azure Storage Explorer** 或从右键单击上下文菜单中选择该命令来打开“Azure 存储资源管理器”。

**打开 Azure 存储资源管理器**

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 **Open Web Azure Storage Explorer**，或在脚本编辑器中的相对路径或完整路径上右键单击，并选择“打开 Web Azure 存储资源管理器”。
3. 选择 Data Lake Analytics 帐户。

Data Lake 工具会在 Azure 门户中打开 Azure 存储路径。 可以从 Web 查找路径和预览文件。

### <a name="local-run-and-local-debug-for-windows-users"></a>Windows 用户的本地运行和本地调试
在将代码发布到 Data Lake Analytics 之前，U-SQL 本地运行可测试本地数据并在本地验证脚本。 使用本地调试功能可以在将代码提交到 Data Lake Analytics 之前完成以下任务： 
- 调试 C# 代码隐藏。 
- 逐步执行代码。 
- 在本地验证脚本。

有关本地运行和本地调试的说明，请参阅[使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)。

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
 
    ![针对 Visual Studio Code 的 Data Lake 工具 IntelliSense 对象类型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   IntelliSense 自动完成 Data Lake Analytics 元数据：Data Lake 工具在本地下载 Data Lake Analytics 元数据信息。 IntelliSense 功能自动填充 Data Lake Analytics 元数据中的对象，包括数据库、架构、表、视图、表值函数、过程和 C# 程序集。
 
    ![针对 Visual Studio Code 的 Data Lake 工具 IntelliSense 元数据](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense 错误标记：Data Lake 工具使用下划线标出 U-SQL 和 C# 编辑错误。 
-   语法突出显示：Data Lake 工具使用不同颜色来区分变量、关键字、数据类型和函数等项。 

    ![针对 Visual Studio Code 的 Data Lake 工具语法突出显示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>后续步骤

- 对于使用 Visual Studio Code 进行的 U-SQL 本地运行和本地调试，请参阅[使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)。
- 有关 Data Lake Analytics 的入门信息，请参阅[教程：Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。
- 有关用于 Visual Studio 的 Data Lake 工具的信息，请参阅[教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
- 有关开发程序集的信息，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集](data-lake-analytics-u-sql-develop-assemblies.md)。




