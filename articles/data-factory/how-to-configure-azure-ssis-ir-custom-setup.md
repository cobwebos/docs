---
title: 自定义 Azure-SSIS Integration Runtime 的设置
description: 本文介绍如何使用 Azure-SSIS Integration Runtime 的自定义安装界面来安装其他组件或更改设置
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
ms.openlocfilehash: c4502cc4a808b4a44f70c1f96fe38fd0ddeebdee
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187717"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>自定义 Azure-SSIS Integration Runtime 的设置

Azure SQL Server Integration Services Integration Runtime （Azure-SSIS IR）的自定义安装程序提供了一个接口，用于在设置或重新配置 Azure-SSIS IR 时添加自己的步骤。 

使用自定义安装，你可以将默认的操作配置或环境更改为，例如，启动其他 Windows 服务或持久保存文件共享的访问凭据。 或者，你可以在 Azure-SSIS IR 的每个节点上安装其他组件，如程序集、驱动程序或扩展。

可以通过以下两种方式之一在 Azure-SSIS IR 上执行自定义设置： 
* **无脚本的快速自定义安装**：运行一些常见的系统配置和 Windows 命令，或安装一些常用或推荐的其他组件，而无需使用任何脚本。
* **带有脚本的标准自定义设置**：准备脚本及其关联的文件，并将它们全部上传到 Azure 存储帐户中的 blob 容器。 然后，在设置或重新配置 Azure-SSIS IR 时，为容器提供共享访问签名（SAS）统一资源标识符（URI）。 然后，Azure-SSIS IR 的每个节点从容器下载脚本及其关联的文件，并以提升的权限运行自定义安装程序。 自定义安装完成后，每个节点会将执行的标准输出和其他日志上传到容器中。

可以通过快速和标准自定义设置来安装免费的、未经许可的组件以及付费的许可组件。 如果你是独立软件供应商（ISV），请参阅[开发 Azure-SSIS IR 的付费或许可组件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> 由于 Azure-SSIS IR 的 v2 系列节点不适用于自定义安装，因此请改用 v3 系列节点。 如果已在使用 v2 系列节点，请尽快切换到 v3 系列节点。

## <a name="current-limitations"></a>当前限制

以下限制仅适用于标准自定义设置：

- 如果要在脚本中使用*gacutil.exe*在全局程序集缓存（GAC）中安装程序集，则需要在自定义安装过程中提供*gacutil.exe。* 或者，你可以使用我们的*公共预览版*容器中提供的副本，稍后将在 "说明" 部分中进行讨论。

- 如果要在脚本中引用子文件夹， *msiexec*不支持使用 `.\` 表示法来引用根文件夹。 使用诸如 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 的命令而不是 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

- Azure-SSIS IR 上当前不支持 Windows 自动创建的管理共享或隐藏的网络共享。

- Azure-SSIS IR 上不支持 IBM iSeries Access ODBC 驱动程序。 你可能会在自定义安装过程中看到安装错误。 如果这样做，请联系 IBM 支持部门以获得帮助。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自定义 Azure-SSIS IR，需要以下项：

- [Azure 订阅](https://azure.microsoft.com/)

- [预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 对于快速自定义设置，则不是必需的。 对于标准自定义设置，请在 blob 容器中上传和存储自定义安装脚本及其关联的文件。 自定义安装进程还会将其执行日志上传到相同的 Blob 容器。

## <a name="instructions"></a>Instructions

1. 如果要在 PowerShell 中设置或重新配置 Azure-SSIS IR，请下载并安装[Azure PowerShell](/powershell/azure/install-az-ps)。 对于快速自定义设置，请跳到步骤4。

1. 准备自定义安装脚本及其关联的文件（例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 文件）。

   * 必须有一个名为 " *main*" 的脚本文件，该文件是自定义安装的入口点。  
   * 若要确保脚本可以悄悄地执行，建议先在本地计算机上对其进行测试。  
   * 如果要将其他工具（例如*msiexec*）生成的其他日志上传到容器，请将预定义的环境变量 `CUSTOM_SETUP_SCRIPT_LOG_DIR`指定为脚本中的日志文件夹（例如， *msiexec/i xxx/lv% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install*）。

1. 下载、安装和打开[Azure 存储资源管理器](https://storageexplorer.com/)。 为此，请执行以下操作：

   a. 在 " **（本地和附加）** " 下，右键单击 "**存储帐户**"，然后选择 "**连接到 Azure 存储**"。

      ![连接到 Azure 存储空间](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 选择 "**使用存储帐户名称和密钥**"，然后选择 "**下一步**"。

      ![使用存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. 输入 Azure 存储帐户名称和密钥，选择 "**下一步**"，然后选择 "**连接**"。

      ![提供存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 在连接的 Azure 存储帐户下，右键单击 " **Blob 容器**"，选择 "**创建 blob 容器**"，并为新容器命名。

      ![创建 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 选择新容器，并上传自定义安装脚本及其关联的文件。 请确保在容器的顶级上传*main .cmd* ，而不是在任何文件夹中上传。 还要确保容器只包含必要的自定义安装文件，以便稍后将其下载到 Azure-SSIS IR 不会花费很长时间。 自定义安装程序的最长持续时间当前设置为在45分钟后超时。这包括从容器下载所有文件并将其安装到 Azure-SSIS IR 的时间。 如果安装程序需要更多时间，则提出支持票证。

      ![将文件上传到 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 右键单击容器，然后选择 "**获取共享访问签名**"。

      ![获取容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 使用足够长的到期时间和读/写/列表权限创建容器的 SAS URI。 只要重置映像或重新启动了 Azure-SSIS IR 的任何节点，就需要 SAS URI 才能下载并运行自定义安装脚本及其关联的文件。 需要拥有写入权限才能上传安装执行日志。

      > [!IMPORTANT]
      > 请确保 SAS URI 不会过期，并且自定义安装程序资源在 Azure-SSIS IR 的整个生命周期（从创建到删除）中始终可用，尤其是在这段时间内定期停止并启动 Azure-SSIS IR 的情况。

      ![生成容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 复制并保存容器的 SAS URI。

      ![复制并保存共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 使用数据工厂 UI 设置或重新配置 Azure-SSIS IR 时，可以通过在 "**集成运行时设置**" 窗格的 "**高级设置**" 部分中选择 "**使用其他系统配置/组件安装自定义 Azure-SSIS Integration Runtime** " 复选框来添加或删除自定义设置。 

   如果要添加标准自定义设置，请在 "**自定义安装容器 SAS uri** " 框中输入容器的 SAS uri。 
   
   如果要添加快速自定义安装，请选择 "**新建**" 以打开 "**添加快速自定义安装程序**" 窗格，然后在 "**快速自定义安装类型**" 下拉列表中选择一种类型：

   * 如果选择 "**运行 cmdkey" 命令**类型，则可以在 Azure-SSIS IR 上保存文件共享或 Azure 文件共享的访问凭据，方法是在 **/add**、 **/user**和 **/Pass**框中输入目标计算机名称或域名、帐户名或用户名以及帐户密钥或密码。 这类似于在本地计算机上运行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey)命令。
   
   * 如果选择 "**添加环境变量**类型"，则可以通过在 "**变量名称**" 和 "**变量值**" 框中输入环境变量名称和值，添加要在 Azure-SSIS IR 上运行的包中使用的 Windows 环境变量。 这类似于在本地计算机上运行 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1)命令。

   * 如果选择 "**安装许可的组件**类型"，则可以在 "**组件名称**" 下拉列表中选择 ISV 合作伙伴提供的集成组件：

     * 如果选择**SentryOne 的任务工厂**组件，则可以通过在 "**许可证密钥**" 框中输入你从中购买的产品许可证密钥，从 Azure-SSIS IR 上的 SentryOne 安装组件的[任务工厂](https://www.sentryone.com/products/task-factory/high-performance-ssis-components)套件。 当前的集成版本为**2019.4.3**。

     * 如果选择**OH22'S HEDDA。IO**组件，可以安装[HEDDA。](https://hedda.io/ssis-component/)在购买其服务后 Azure-SSIS IR 上的 oh22 中的 IO 数据质量/清理组件。 当前的集成版本为**1.0.13**。

      * 如果选择**oh22's SQLPhonetics.NET**组件，则可以通过在 "**许可证密钥**" 框中输入你从中购买的产品许可证密钥，在 Azure-SSIS IR 上的 oh22 中安装[SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/)数据质量/匹配组件。 当前的集成版本为**1.0.43**。
   
   你添加的快速自定义设置将显示在 "**高级设置**" 部分中。 若要删除它们，请选中其复选框，然后选择 "**删除**"。

   ![自定义设置的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. 使用 PowerShell 设置或重新配置 Azure-SSIS IR 时，可以在启动 Azure-SSIS IR 之前，通过运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 来添加或删除自定义设置。
   
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
   
   标准自定义安装程序完成并且 Azure-SSIS IR 启动后，可以在存储容器的*主*文件夹中找到*main .cmd*和其他执行日志的标准输出。

1. 若要查看某些标准自定义设置的示例，请使用 Azure 存储资源管理器连接到公共预览版容器。

   a. 在“(本地和附加)”下面，右键单击“存储帐户”，并依次选择“连接到 Azure 存储”、“使用连接字符串或共享访问签名 URI”、“下一步”。

      ![使用共享访问签名连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. 选择 "**使用 SAS uri** "，然后在 " **URI** " 框中输入以下 SAS uri：

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![提供容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 选择 "**下一步**"，然后选择 "**连接**"。

   d. 在左窗格中，选择 "已连接的**publicpreview** " 容器，然后双击 " *CustomSetupScript* " 文件夹。 此文件夹包含以下项：

      * 一个*示例*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装基本任务的自定义安装程序。 该任务不会执行任何操作，而是休眠几秒。 该文件夹还包含一个*gacutil.exe*文件夹，其整个内容（*gacutil.exe*、 *gacutil.exe*和*1033 \ gacutlrc*）可以按原样复制到容器中。

      * 一个*UserScenarios*文件夹，其中包含来自真实用户方案的多个自定义安装示例。

        ![公共预览版容器的内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. 双击 " *UserScenarios* " 文件夹以查找以下项：

      * 一个 *.NET FRAMEWORK 3.5*文件夹，其中包含一个自定义安装程序，用于安装 Azure-SSIS IR 的每个节点上的自定义组件可能需要的 .NET Framework 的早期版本。

      * *BCP*文件夹，其中包含要在 Azure-SSIS IR 的每个节点上安装 SQL Server 命令行实用程序（*MsSqlCmdLnUtils*）的自定义安装程序，包括大容量复制程序（*BCP*）。

      * 一个*EXCEL*文件夹，其中包含用于安装C#程序集和库的自定义安装脚本（*main .cmd*），可以在脚本任务中使用这些程序集和库在 Azure-SSIS IR 的每个节点上动态读取和写入 EXCEL 文件。 
      
        首先，下载[*ExcelDataReader*](https://www.nuget.org/packages/ExcelDataReader/)和[*DocumentFormat*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)，然后将它们与*main .cmd*一起上传到容器中。 或者，如果您只想使用标准的 Excel 连接管理器、Excel 源和 Excel 目标，则在您的 Azure-SSIS IR 上预先安装了所需的访问可再发行组件，因此不需要进行任何自定义设置。
      
      * *MYSQL odbc*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 MYSQL odbc 驱动程序的自定义安装脚本（*main .cmd*）。 此设置允许你使用 ODBC 连接管理器、源和目标来连接 MySQL 服务器。 
     
        首先，[下载最新的64位和32位版本的 MYSQL ODBC 驱动程序安装程序](https://dev.mysql.com/downloads/connector/odbc/)（例如， *mysql-connector-odbc-8.0.13-winx64*和*mysql-connector-odbc-8.0.13-win32*），然后将所有这些版本连同*main .cmd*一起上传到容器中。

      * *ORACLE ENTERPRISE*文件夹，其中包含自定义安装脚本（*main .cmd*）和无提示安装配置*文件（OCI*），用于在 Azure-SSIS IR ENTERPRISE Edition 的每个节点上安装 ORACLE 连接器和驱动程序。 此设置允许你使用 Oracle 连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，从[Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=55179)下载 microsoft connector v 5.0 （*AttunitySSISOraAdaptersSetup*和*AttunitySSISOraAdaptersSetup64*），并从[oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)下载最新的 oracle 客户端（例如*winx64_12102_client .zip*），然后将所有这些*连接器与*和 node.js 一起上*传到容器*中。 如果使用 TNS 连接到 Oracle，还需要下载*tnsnames.ora*，对其进行编辑，并将其上传到容器，以便在安装过程中将其复制到 Oracle 安装文件夹中。

      * *ORACLE STANDARD ADO.NET*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 ORACLE ODP.NET 驱动程序的自定义安装脚本（*main .cmd*）。 此设置允许你使用 ADO.NET 连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，[下载最新的 Oracle ODP.NET 驱动程序](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)（例如， *NET_Managed_ODAC122cR1 .zip*），然后将其连同*main .cmd*一起上载到容器。
       
      * *ORACLE 标准 ODBC*文件夹，其中包含用于安装 ORACLE ODBC 驱动程序的自定义安装脚本（*main .cmd*），并在 Azure-SSIS IR 的每个节点上配置数据源名称（DSN）。 此设置允许你使用 ODBC 连接管理器、源和目标或 Power Query 连接管理器和源与 ODBC 数据源类型连接到 Oracle 服务器。 
      
        首先，下载最新的 Oracle 即时客户端（基本包或基本 Lite 包）和 ODBC 包，然后将它们与*main. .cmd*一起上传到容器：
        * [下载64位包](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)（基本包： *instantclient-basic-windows. x64-18.3.0.0.0 dbru*;基本精简包： *instantclient-basiclite-windows. x64-18.3.0.0.0 dbru*;ODBC 包： *instantclient-odbc-windows. x64-18.3.0.0.0 dbru*） 
        * [下载32位包](https://www.oracle.com/technetwork/topics/winsoft-085727.html)（基本包： *instantclient-basic-nt-18.3.0.0.0 dbru*;基本精简包： *instantclient-basiclite-nt-18.3.0.0.0 dbru*;ODBC 包： *instantclient-odbc-nt-18.3.0.0.0 dbru*）

      * *ORACLE 标准 OLEDB*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 ORACLE OLEDB 驱动程序的自定义安装脚本（*main .cmd*）。 此设置允许你使用 OLEDB 连接管理器、源和目标来连接到 Oracle 服务器。 
     
        首先，[下载最新的 ORACLE OLEDB 驱动程序](https://www.oracle.com/partners/campaign/index-090165.html)（例如*ODAC122010Xcopy_x64 .zip*），然后将其连同*main .cmd*一起上载到容器。

      * *POSTGRESQL ODBC*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 POSTGRESQL ODBC 驱动程序的自定义安装脚本（*main .cmd*）。 此设置允许你使用 ODBC 连接管理器、源和目标连接到 PostgreSQL 服务器。 
     
        首先，[下载 POSTGRESQL ODBC 驱动程序安装程序的最新64位和32位版本](https://www.postgresql.org/ftp/odbc/versions/msi/)（例如*psqlodbc_x64 .msi*和*psqlodbc_x86 .msi*），然后将所有这些版本连同*main .cmd*一起上传到容器中。

      * 一个*SAP BW*文件夹，其中包含一个自定义安装脚本 *（librfc32.dll），* 用于在 Azure-SSIS IR Enterprise Edition 的每个节点上安装 SAP .net 连接器程序集（）。 此设置允许你使用 SAP Business 仓库（BW）连接管理器、源和目标连接到 SAP BW 服务器。 
      
        首先，将*librfc32.dll*的64位或32位版本从 SAP 安装文件夹中上*传到容器中。* 然后，该脚本会在安装过程中将 SAP 程序集复制到 *%windir%\SysWow64*或 *%windir%\System32*文件夹。

      * *存储*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Azure PowerShell 的自定义安装程序。 此设置使你可以部署和运行运行 PowerShell 脚本的 SSIS 包[以操作 Azure 存储帐户](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 
      
        将*AzurePowerShell* （或使用最新版本）和*存储空间* *复制到容器中。* 使用 *.dtsx*作为包的模板。 包模板结合了[Azure Blob 下载任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)，可将*存储*下载为可修改的 PowerShell 脚本和[执行进程任务](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)，该任务将在每个节点上执行脚本。

      * 一个*TERADATA*文件夹，其中包含一个自定义安装脚本（*main .cmd*）、其关联的文件（*install .cmd*）以及安装程序包（ *.msi*）。 这些文件在 Azure-SSIS IR Enterprise Edition 的每个节点上安装 Teradata 连接器、Teradata Parallel Transporter （TPT） API 和 ODBC 驱动程序。 此设置允许你使用 Teradata 连接管理器、源和目标连接到 Teradata 服务器。 
      
        首先，[下载 Teradata 工具和实用程序](http://partnerintelligence.teradata.com)的第15个文件（例如 TeradataToolsAndUtilitiesBase__windows_indep， *15.10.22.00*），并将其与前面提到的 *.cmd*和 *.msi*文件一起上载到容器。

      * 一个*祖鲁 OPENJDK*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装祖鲁 OPENJDK 的自定义安装脚本（*main .Cmd*）和 PowerShell 文件（*install_openjdk ps1*）。 此设置使你可以使用 Azure Data Lake Store 和灵活的文件连接器来处理 ORC 和 Parquet 文件。 有关详细信息，请参阅[适用于 Integration Services 的 Azure 功能包](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 
      
        首先，[下载最新的祖鲁 OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) （例如，*祖鲁 8.33.0.1-jdk 8.0.192 win_x64 .zip*），然后将其连同 main *install_openjdk* *.cmd*一起上传到容器中。

        ![用户方案文件夹中的文件夹](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 若要尝试这些自定义安装示例，请将所选文件夹中的内容复制到容器中。
   
      使用数据工厂 UI 设置或重新配置 Azure-SSIS IR 时，请选中 "**高级设置**" 部分中的 "**使用其他系统配置/组件安装自定义 Azure-SSIS Integration Runtime** " 复选框，然后在 "**自定义安装容器 sas uri** " 框中输入容器的 SAS uri。
   
      通过 PowerShell 设置或重新配置 Azure-SSIS IR 时，请以容器的 SAS URI 作为 `SetupScriptContainerSasUri` 参数的值运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet。

## <a name="next-steps"></a>后续步骤

- [设置 Azure-SSIS Integration Runtime 的 Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [为 Azure-SSIS Integration Runtime 开发付费或许可的自定义组件](how-to-develop-azure-ssis-ir-licensed-components.md)
