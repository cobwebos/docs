---
title: 自定义 Azure-SSIS 集成运行时的安装
description: 本文介绍如何使用 Azure-SSIS 集成运行时的自定义安装界面，安装其他组件或更改设置
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/01/2020
ms.openlocfilehash: e85ef22542fc162648dbfc637892cf7e580c6aac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964543"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>自定义 Azure-SSIS 集成运行时的安装

Azure-SSIS 集成运行时的自定义安装界面提供了一个界面，用于在预配或重新配置 Azure-SSIS IR 期间添加自己的安装步骤。 

使用自定义安装程序可以更改默认的操作配置或环境（例如，用于启动其他 Windows 服务，或保存访问凭据以便进行文件共享），或者在 Azure-SSIS IR 的每个节点上安装其他组件（例如程序集、驱动程序或扩展）。

可以通过两种方法在 Azure-SSIS IR 上执行自定义设置：无需脚本的快速自定义设置和带脚本的标准自定义设置。

使用快速自定义安装，你可以运行一些常见的系统配置/Windows 命令，或安装一些受欢迎/推荐的其他组件，而无需使用任何脚本。  

使用标准自定义设置，你需要准备脚本及其关联的文件，并将它们全部上传到 Azure 存储帐户中的 blob 容器中。 然后，在设置或重新配置 Azure-SSIS IR 时，为容器提供共享访问签名（SAS）统一资源标识符（URI）。 然后，你的 Azure-SSIS IR 的每个节点都将从你的容器下载脚本及其关联的文件，并通过提升的权限运行自定义安装程序。 自定义安装完成后，每个节点会将执行的标准输出和其他日志上传到容器中。

你可以通过 "快速/标准" 自定义设置安装免费/未经许可和付费/许可组件。 如果你是 ISV，请参阅有关[如何为 Azure-SSIS IR 开发付费或许可组件的](how-to-develop-azure-ssis-ir-licensed-components.md)文档。

> [!IMPORTANT]
> Azure-SSIS IR 的 v2 系列节点不适用于自定义设置，因此请改用 v3 系列节点。  如果已使用 v2 系列节点，请尽快改为使用 v3 系列节点。

## <a name="current-limitations"></a>当前限制

以下限制仅适用于标准自定义设置：

- 如果要在脚本中使用 `gacutil.exe` 在全局程序集缓存（GAC）中安装程序集，则需要提供 `gacutil.exe` 作为自定义安装的一部分，或者使用以下公共预览版容器中提供的副本。

- 如果要在脚本中引用子文件夹，`msiexec.exe` 不支持使用 `.\` 表示法引用根文件夹。 请使用类似 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 的命令，而不要使用 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

- 当前不支持在 Azure-SSIS IR 上使用管理共享（即 Windows 自动创建的隐藏网络共享）。

- Azure-SSIS IR 上不支持 IBM iSeries Access ODBC 驱动程序。 你可能会在自定义安装过程中看到安装错误。 请联系 IBM 支持部门以获得帮助。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自定义 Azure-SSIS IR，需要做好以下准备：

- [Azure 订阅](https://azure.microsoft.com/)

- [预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 对于快速自定义设置，则不是必需的。 对于标准自定义设置，请在 blob 容器中上传和存储自定义安装脚本及其关联的文件。 自定义安装进程还会将其执行日志上传到相同的 Blob 容器。

## <a name="instructions"></a>Instructions

1. 如果要通过 PowerShell 预配或重新配置 Azure-SSIS IR，请下载并安装[Azure PowerShell](/powershell/azure/install-az-ps)。 对于快速自定义设置，请跳到步骤4。

1. 准备自定义安装脚本及其关联的文件（例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 文件）。

   1. 必须创建一个名为 `main.cmd` 的脚本文件，即自定义安装程序的入口点。

   1. 你需要确保脚本可以在无提示的情况下执行，建议先在本地计算机上测试脚本。

   1. 如果想要将其他工具（例如 `msiexec.exe`）生成的其他日志上传到容器，请在脚本中指定预定义的环境变量 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 作为日志文件夹（例如 `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`）。

1. 下载、安装并启动 [Azure 存储资源管理器](https://storageexplorer.com/)。

   1. 在“(本地和附加)”下面，右键单击“存储帐户”并选择“连接到 Azure 存储”。

      ![连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. 选择“使用存储帐户名和密钥”并选择“下一步”。

      ![使用存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. 输入 Azure 存储帐户名和密钥，并依次选择“下一步”、“连接”。

      ![提供存储帐户名和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. 在已连接的 Azure 存储帐户下面，右键单击“Blob 容器”，选择“创建 Blob 容器”，并为新容器命名。

      ![创建 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. 选择新容器并上传自定义安装脚本及其关联的文件。 请务必将 `main.cmd` 上传到容器的顶级目录，而不要上传到任何文件夹中。 另请确保你的容器仅包含所需的自定义安装文件，以便稍后将它们下载到 Azure-SSIS IR 时不会花费较长的时间。 自定义安装程序的最长时间当前设置为在45分钟后超时，这包括从容器下载所有文件并将其安装到 Azure-SSIS IR 的时间。 如果需要更长的时间段，请提出支持票证。

      ![将文件上传到 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. 右键单击容器，并选择“获取共享访问签名”。

      ![获取容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. 为容器创建一个过期时间足够长且具有读取 + 写入 + 列出权限的 SAS URI。 每当将 Azure-SSIS IR 的任何节点重置映像/重启时，都需要使用该 SAS URI 来下载并运行自定义安装脚本及其关联文件。 需要拥有写入权限才能上传安装执行日志。

      > [!IMPORTANT]
      > 请确保 SAS URI 不会过期，自定义安装资源始终在 Azure-SSIS IR 的整个生命周期（从创建到删除）中可用，尤其是在此期间定期停止并启动 Azure-SSIS IR 时。

      ![生成容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. 复制并保存容器的 SAS URI。

      ![复制并保存共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 使用数据工厂 UI 设置或重新配置 Azure-SSIS IR 时，可以通过在集成运行时设置面板的 "**高级设置**" 部分中选择 "**使用其他系统配置自定义 Azure-SSIS Integration Runtime** " 复选框来添加/删除自定义设置。 

   如果要添加标准自定义设置，请在 "**自定义安装容器 SAS uri** " 字段中输入容器的 SAS uri。 
   
   如果要添加快速自定义设置，请选择 "**新建**" 以打开 "**添加自定义" 安装**面板，然后在 "**快速自定义安装类型**" 下拉菜单下选择任意类型：

   1. 如果选择 "**运行 cmdkey" 命令**类型，则可以在 " **/add**"、" **/user**" 和 " **/Pass** " 字段中分别输入目标计算机/域名、帐户名称/用户名和帐户密钥/密码，从而在 Azure-SSIS IR 上保存文件共享/Azure 文件的访问凭据。 这类似于在本地计算机上运行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey)命令。
   
   1. 如果选择 "**添加环境变量**类型"，则可以通过在 "**变量名称**" 和 "**变量值**" 字段中分别输入环境变量名称和值，添加要在 Azure-SSIS IR 上运行的包中使用的 Windows 环境变量。 这类似于在本地计算机上运行 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1)命令。

   1. 如果选择 "**安装许可的组件**类型"，则可以在 "**组件名称**" 下拉菜单下选择 ISV 合作伙伴提供的任何集成组件：

      1. 如果选择**SentryOne 的任务工厂**组件，则可以通过在 "**许可证密钥**" 字段中输入你从中购买的产品许可证密钥，从 Azure-SSIS IR 上的 SentryOne 安装组件的[任务工厂](https://www.sentryone.com/products/task-factory/high-performance-ssis-components)套件。 当前的集成版本为**2019.4.3**。

      1. 如果选择**OH22'S HEDDA。IO**组件，可以安装[HEDDA。](https://hedda.io/ssis-component/)在购买其服务后 Azure-SSIS IR 上的 oh22 中的 IO 数据质量/清理组件。 当前的集成版本为**1.0.13**。

      1. 如果选择**oh22's SQLPhonetics.NET**组件，则可以通过在 "**许可证密钥**" 字段中输入你从中购买的产品许可证密钥，在 Azure-SSIS IR 上的 oh22 中安装[SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/)数据质量/匹配组件。 当前的集成版本为**1.0.43**。
   
   你添加的快速自定义设置将显示在 "**高级设置**" 部分中。 若要删除它们，可以选中其复选框，然后选择 "**删除**"。

   ![自定义设置的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. 使用 PowerShell 设置或重新配置 Azure-SSIS IR 时，可以在启动 Azure-SSIS IR 之前，通过运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 来添加/删除自定义设置。
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   标准自定义安装程序完成并且 Azure-SSIS IR 启动后，可以在存储容器的 `main.cmd.log` 文件夹中找到 `main.cmd` 和其他执行日志的标准输出。

1. 若要查看标准自定义设置的一些示例，请通过 Azure 存储资源管理器连接到公共预览版容器。

   1. 在“(本地和附加)”下面，右键单击“存储帐户”，并依次选择“连接到 Azure 存储”、“使用连接字符串或共享访问签名 URI”、“下一步”。

      ![使用共享访问签名连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. 选择“使用 SAS URI”并输入公共预览版容器的以下 SAS URI。 选择 "**下一步**"，然后选择 "**连接**"。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![提供容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. 选择已连接的公共预览版容器，并双击 `CustomSetupScript` 文件夹。 此文件夹包含以下项：

      1. 一个 `Sample` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装基本任务的自定义安装程序。 该任务不会执行任何操作，而是休眠几秒。 该文件夹还包含 `gacutil` 文件夹，其整个内容（`gacutil.exe`、`gacutil.exe.config` 和 `1033\gacutlrc.dll`）都可以按原样复制到容器中。

      1. 一个 `UserScenarios` 文件夹，其中包含来自真实用户方案的多个自定义安装示例。

      ![公共预览版容器的内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. 双击 `UserScenarios` 文件夹以查找以下项：

      1. 一个 `.NET FRAMEWORK 3.5` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装自定义组件可能需要的 .NET Framework 早期版本的自定义安装程序。

      1. 一个 `BCP` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 SQL Server 命令行实用工具 (`MsSqlCmdLnUtils.msi`)（包括批量复制程序 (`bcp`)）的自定义安装程序。

      1. 一个 `EXCEL` 文件夹，其中包含用于安装C#程序集/库的自定义安装脚本（`main.cmd`），可以在脚本任务中使用该脚本来动态读取/写入 Azure-SSIS IR 的每个节点上的 Excel 文件。 首先，从[此处](https://www.nuget.org/packages/ExcelDataReader/)下载 `ExcelDataReader.dll`，并从[此处](https://www.nuget.org/packages/DocumentFormat.OpenXml/)`DocumentFormat.OpenXml.dll`，然后将其与 `main.cmd` 一起上传到容器中。 或者，如果只是想要使用标准的 Excel 连接管理器/源/目标，则已在 Azure-SSIS IR 上预先安装了所需的访问可再发行组件，因此不需要进行任何自定义设置。
      
      1. 一个 `MYSQL ODBC` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 MySQL ODBC 驱动程序的自定义安装脚本（`main.cmd`）。 此设置允许你使用 ODBC 连接管理器/源/目标连接到 MySQL 服务器。 首先，下载最新的64位和32位版本的 MySQL ODBC 驱动程序安装程序（例如，从[MySQL](https://dev.mysql.com/downloads/connector/odbc/)`mysql-connector-odbc-8.0.13-winx64.msi` 和 `mysql-connector-odbc-8.0.13-win32.msi`），然后将其与 `main.cmd` 一起上传到容器中。

      1. 一个 `ORACLE ENTERPRISE` 文件夹，其中包含用于在 Azure-SSIS IR 企业版的每个节点上安装 Oracle 连接器和 OCI 驱动程序的自定义安装脚本 (`main.cmd`) 和无提示安装配置文件 (`client.rsp`)。 此设置允许你使用 Oracle 连接管理器/源/目标连接到 Oracle 服务器。 首先，从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=55179)下载适用于 Oracle 的 Microsoft 连接器 v5.0 （`AttunitySSISOraAdaptersSetup.msi` 和 `AttunitySSISOraAdaptersSetup64.msi`），并从 [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 下载最新的 Oracle 客户端，例如 `winx64_12102_client.zip`，然后将它们连同 `main.cmd` 和 `client.rsp` 一起上传到你的容器中。 如果使用 TNS 连接到 Oracle，则还需要下载 `tnsnames.ora`，对其进行编辑，然后将其上传到容器，以便在安装期间将其复制到 Oracle 安装文件夹中。

      1. 一个 `ORACLE STANDARD ADO.NET` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODP.NET 驱动程序的自定义安装脚本 (`main.cmd`)。 此设置允许你使用 ADO.NET 连接管理器/源/目标连接到 Oracle 服务器。 首先，下载最新的 Oracle ODP.NET 驱动程序（例如 `ODP.NET_Managed_ODAC122cR1.zip`-从[Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)），然后将其与 `main.cmd` 一起上传到容器中。
       
      1. 一个 `ORACLE STANDARD ODBC` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODBC 驱动程序并配置 DSN 的自定义安装脚本 (`main.cmd`)。 此安装程序允许你使用 ODBC 连接管理器/源/目标或 Power Query 连接管理器/源以及 ODBC 数据源种类来连接到 Oracle 服务器。 首先，下载最新的 Oracle 即时客户端（基本包或基本精简包）和 ODBC 包-例如[此处](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)的64位包（基本包： `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`、基本 lite 包： `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`、odbc 包： `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`）或32位[包（基本](https://www.oracle.com/technetwork/topics/winsoft-085727.html)包： `instantclient-basic-nt-18.3.0.0.0dbru.zip`，基本精简包： `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`，odbc package： `instantclient-odbc-nt-18.3.0.0.0dbru.zip`），然后将其与 `main.cmd` 一起上传到容器中。

      1. 一个 `ORACLE STANDARD OLEDB` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle OLEDB 驱动程序的自定义安装脚本（`main.cmd`）。 此设置允许你使用 OLEDB 连接管理器/源/目标连接到 Oracle 服务器。 首先，下载最新的 Oracle OLEDB 驱动程序（例如从[oracle](https://www.oracle.com/partners/campaign/index-090165.html)`ODAC122010Xcopy_x64.zip`），然后将其与 `main.cmd` 一起上传到容器中。

      1. 一个 `POSTGRESQL ODBC` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 PostgreSQL ODBC 驱动程序的自定义安装脚本（`main.cmd`）。 此设置允许你使用 ODBC 连接管理器/源/目标连接到 PostgreSQL 服务器。 首先，下载 PostgreSQL ODBC 驱动程序安装程序的最新64位和32位版本（例如 `psqlodbc_x64.msi` 和 `psqlodbc_x86.msi`），然后将 `main.cmd` 全部上传到[容器中。](https://www.postgresql.org/ftp/odbc/versions/msi/)

      1. 一个 `SAP BW` 文件夹，其中包含用于在 Azure-SSIS IR Enterprise Edition 的每个节点上安装 SAP .NET 连接器程序集（`librfc32.dll`）的自定义安装脚本（`main.cmd`）。 此设置允许你使用 SAP BW 连接管理器/源/目标连接到 SAP BW 服务器。 首先，将 SAP 安装文件夹中的64位或32位版本的 `librfc32.dll` 上传到容器中 `main.cmd`。 然后，该脚本会在安装期间将 SAP 程序集复制到 `%windir%\SysWow64` 或 `%windir%\System32` 文件夹中。

      1. 一个 `STORAGE` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Azure PowerShell 的自定义安装程序。 此安装程序允许部署并运行 SSIS 包，以便运行 [PowerShell 脚本来操作 Azure 存储帐户](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 将 `main.cmd`、示例 `AzurePowerShell.msi` （或使用最新版本）和 `storage.ps1` 复制到容器。 使用 PowerShell.dtsx 作为包的模板。 包模板中合并了 [Azure Blob 下载任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)（包括可修改 PowerShell 脚本形式的 `storage.ps1`）和[执行进程任务](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)（在每个节点上执行脚本）。

      1. 一个 `TERADATA` 文件夹，其中包含自定义安装脚本 (`main.cmd`)、其关联的文件 (`install.cmd`) 和安装程序包 (`.msi`)。 这些文件在 Azure-SSIS IR Enterprise Edition 的每个节点上安装 Teradata 连接器、TPT API 和 ODBC 驱动程序。 此设置允许你使用 Teradata 连接管理器/源/目标连接到 Teradata 服务器。 首先，从[Teradata](http://partnerintelligence.teradata.com)下载 Teradata 工具和实用工具第 15. x zip 文件（例如 `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`），然后将其与上述 `.cmd` 一起上传，并将 `.msi` 文件上传到容器中。

      1. 一个 `ZULU OPENJDK` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装祖鲁 OpenJDK 的自定义安装脚本（`main.cmd`）和 PowerShell 文件（`install_openjdk.ps1`）。 此设置允许你使用 Azure Data Lake Store/灵活的文件连接器来处理 ORC/Parquet 文件，有关详细信息，请参阅[此处](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 首先，下载最新的祖鲁[OpenJDK （例如 `zulu8.33.0.1-jdk8.0.192-win_x64.zip`），然后](https://www.azul.com/downloads/zulu/zulu-windows/)将其上传 `main.cmd` 并 `install_openjdk.ps1` 到容器中。

      ![用户方案文件夹中的文件夹](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. 若要尝试这些自定义安装示例，请将所选文件夹中的内容复制并粘贴到容器中。
   
      当你为数据工厂 UI 设置或重新配置 Azure-SSIS IR 时，请选中 "**高级设置**" 部分上的 "**自定义你的 Azure-SSIS Integration Runtime 并添加系统配置/组件安装**" 复选框，并在 "**自定义安装容器 sas uri** " 字段中输入你的容器的 SAS uri。
   
      当你在 PowerShell 中设置或重新配置 Azure-SSIS IR 时，请以容器的 SAS URI 作为 `SetupScriptContainerSasUri` 参数的值来运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet。

## <a name="next-steps"></a>后续步骤

-   [Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [如何为 Azure-SSIS 集成运行时开发付费或许可的自定义组件](how-to-develop-azure-ssis-ir-licensed-components.md)
