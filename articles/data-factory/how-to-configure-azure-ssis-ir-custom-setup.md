---
title: 自定义 Azure-SSIS Integration Runtime 的安装
description: 本文介绍如何使用 Azure-SSIS Integration Runtime 的自定义安装界面安装其他组件或更改设置
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
ms.date: 04/15/2020
ms.openlocfilehash: d2a5928d8326c4a0628ebc1bfb7eec3cd20f9254
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747505"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>自定义 Azure-SSIS Integration Runtime 的安装

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) 的自定义安装程序提供了一个界面，用于在安装或重新配置 Azure-SSIS IR 期间添加自己的步骤。 

使用自定义安装程序可以更改默认的操作配置或环境，例如，启动其他 Windows 服务、保存访问凭据以便进行文件共享，或者使用强加密/更安全的网络协议 (TLS 1.2)。 或者，可以在 Azure-SSIS IR 的每个节点上安装其他组件，例如程序集、驱动程序或扩展。

可以通过两种方式在 Azure-SSIS IR 上执行自定义安装： 
* **无需脚本的快速自定义安装**：运行一些常见的系统配置和 Windows 命令，或安装一些流行的或推荐的其他组件，而无需使用任何脚本。
* **使用脚本的标准自定义安装**：准备一个脚本及其关联的文件，并将其全部上传到 Azure 存储帐户中的 Blob 容器。 然后安装或重新配置 Azure-SSIS IR 时，为容器提供共享访问签名 (SAS) 统一资源标识符 (URI)。 然后，Azure-SSIS IR 的每个节点将从该容器下载该脚本及其关联的文件，并使用提升的权限运行自定义安装。 自定义安装完成后，每个节点会将标准执行输出和其他日志上传到容器中。

无论使用快速还是标准自定义安装，都可安装免费（无许可证）和付费（带许可证）的组件。 如果你是独立软件供应商 (ISV)，请参阅[为 Azure-SSIS IR 开发付费或带许可证的组件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> 为了利用将来的增强功能，建议对采用自定义设置的 Azure-SSIS IR 使用 v3 或更高版本的节点系列。

## <a name="current-limitations"></a>当前限制

以下限制仅适用于标准自定义安装：

- 若要在脚本中使用 *gacutil.exe* 将程序集安装到全局程序集缓存 (GAC)，需要在自定义安装过程中提供 *gacutil.exe*。 或者，可以使用稍后在“说明”部分所述的“公共预览版”容器中提供的副本。 

- 若要在脚本中引用某个子文件夹，请注意 *msiexec.exe* 不支持使用 `.\` 表示法引用根文件夹。 请使用类似 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 的命令，而不要使用 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

- Azure-SSIS IR 当前不支持管理共享，或 Windows 自动创建的隐藏的网络共享。

- Azure-SSIS IR 不支持 IBM iSeries Access ODBC 驱动程序。 在自定义安装过程中，可能会出现安装错误。 如果出现，请联系 IBM 支持人员获取帮助。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自定义 Azure-SSIS IR，需要准备好以下各项：

- [Azure 订阅](https://azure.microsoft.com/)

- [预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [一个 Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 对于快速自定义安装并非必需。 对于标准自定义安装，请在 Blob 容器中上传并存储自定义安装脚本及其关联文件。 自定义安装进程还会将其执行日志上传到相同的 Blob 容器。

## <a name="instructions"></a>说明

1. 如果要通过 PowerShell 安装或重新配置 Azure-SSIS IR，请下载并安装 [Azure PowerShell](/powershell/azure/install-az-ps)。 对于快速自定义安装，请跳到步骤 4。

1. 准备自定义安装脚本及其关联的文件（例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 文件）。

   * 必须创建一个名为 *main.cmd* 的脚本文件，即自定义安装程序的入口点。  
   * 为了确保脚本能够以无提示方式执行，我们建议先在本地计算机上对其进行测试。  
   * 若要将其他工具（例如 *msiexec.exe*）生成的其他日志上传到容器，请在脚本中指定预定义的环境变量 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 作为日志文件夹（例如 *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*）。

1. 下载、安装并打开 [Azure 存储资源管理器](https://storageexplorer.com/)。 为此，请执行以下操作：

   a. 在“(本地和附加)”下面，右键单击“存储帐户”并选择“连接到 Azure 存储”。   

      ![连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 依次选择“使用存储帐户名和密钥”、“下一步”。  

      ![使用存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. 输入 Azure 存储帐户名和密钥，并依次选择“下一步”、“连接”。  

      ![提供存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 在已连接的 Azure 存储帐户下面，右键单击“Blob 容器”，选择“创建 Blob 容器”，并为新容器命名。  

      ![创建 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 选择新容器并上传自定义安装脚本及其关联的文件。 请务必将 *main.cmd* 上传到容器的顶级目录，而不要上传到任何文件夹中。 另请确保你的容器仅包含所需的自定义安装文件，以便稍后将这些文件下载到 Azure-SSIS IR 时不会花费太长的时间。 自定义安装的最大持续时间目前设置为 45 分钟，45 分钟后会超时。这包括从容器下载所有文件并将其安装在 Azure-SSIS IR 上所花费的时间。 如果安装需要更长的时间，请提交支持票证。

      ![将文件上传到 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 右键单击容器，并选择“获取共享访问签名”。 

      ![获取容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 为容器创建一个过期时间足够长且拥有读取/写入/列出权限的 SAS URI。 每当将 Azure-SSIS IR 的任何节点重置映像或重启时，都需要使用该 SAS URI 来下载并运行自定义安装脚本及其关联文件。 需要拥有写入权限才能上传安装执行日志。

      > [!IMPORTANT]
      > 确保在 Azure-SSIS IR 的整个生命周期内（从创建到删除），尤其是在此期间你经常停止并启动 Azure-SSIS IR 时，SAS URI 不会过期，并且自定义安装资源始终可用。

      ![生成容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h.如果该值不存在，请单击“添加行”。 复制并保存容器的 SAS URI。

      ![复制并保存共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 使用数据工厂 UI 安装或重新配置 Azure-SSIS IR 时，可以在“集成运行时安装”窗格的“高级设置”部分中选择“使用其他系统配置/组件安装来自定义 Azure-SSIS Integration Runtime”复选框来添加或删除自定义安装。    

   若要添加标准自定义安装，请在“自定义安装容器 SAS URI”框中输入容器的 SAS URI。  
   
   若要添加快速自定义安装，请选择“新建”打开“添加快速自定义安装”窗格，然后在“快速自定义安装类型”下拉列表中选择一种类型：   

   * 如果选择“运行 cmdkey 命令”类型，可以在 Azure-SSIS IR 上保存文件共享或 Azure 文件存储共享的访问凭据，方法是在“/Add”、“/User”和“/Pass”框中输入目标计算机名或域名、帐户名或用户名，以及帐户密钥或密码。     这类似于在本地计算机上运行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) 命令。
   
   * 如果选择“添加环境变量”类型，可在“变量名”和“变量值”框中输入环境变量名称和值，添加要在 Azure-SSIS IR 上运行的包中使用的 Windows 环境变量。    这类似于在本地计算机上运行 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) 命令。

   * 如果选择“安装有许可证的组件”类型，可以在“组件名”下拉列表中选择来自我们 ISV 合作伙伴的任何集成组件：  

     * 如果选择“SentryOne Task Factory”组件，可通过在“许可证密钥”框中输入从 SentryOne 购买的产品许可证密钥，在 Azure-SSIS IR 上安装 SentryOne 提供的 [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 组件套件。   当前的集成版本是 2019.4.3  。

     * 如果选择“oh22 的 HEDDA.IO”组件，可在购买 oh22 服务后，在 Azure-SSIS IR 上安装 oh22 的 [HEDDA.IO](https://hedda.io/ssis-component/) 数据质量/清理组件  。 当前的集成版本是 1.0.13  。

     * 如果选择“oh22 的 SQLPhonetics.NET”组件，可通过在“许可证密钥”框中输入从 oh22 购买的产品许可证密钥，在 Azure-SSIS IR 上安装 oh22 的 [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) 数据质量/匹配组件。   当前的集成版本是 1.0.43  。

     * 如果选择“KingswaySoft 的 SSIS Integration Toolkit”组件，可以通过在“许可密钥”框中输入从 KingswaySoft 购买的产品许可密钥，在 Azure-SSIS IR 上安装适用于 KingswaySoft 所提供的 CRM/ERP/营销/协作应用（例如 Microsoft Dynamics/SharePoint/Project Server、Oracle/Salesforce Marketing Cloud 等）的 [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) 套件。   当前的集成版本是 **2019.2**。

     * 如果选择“KingswaySoft 的 SSIS Productivity Pack”组件，可以通过在“许可密钥”框中输入从 KingswaySoft 购买的产品许可密钥，在 Azure-SSIS IR 上安装 KingswaySoft 所提供的组件的 [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) 套件。   当前的集成版本是 **10.0**。

     * 如果选择“Theobald Software 的 Xtract IS”  组件，可以通过将从 Theobald Software 购买的产品许可证文件拖放/上传到“许可证文件”  框中，在 Azure-SSIS IR 上安装 Theobald Software 所提供的 SAP 系统（ERP、S/4HANA、BW）的 [Xtract IS](https://theobald-software.com/en/xtract-is/) 连接器套件。 当前的集成版本是 6.1.1.3  。

   添加的快速自定义安装将显示在“高级设置”部分。  若要删除它们，请选中其复选框，然后选择“删除”。 

   ![包含自定义安装的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. 使用 PowerShell 安装或重新配置 Azure-SSIS IR 时，可以在启动 Azure-SSIS IR 之前，运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 来添加或删除自定义安装。
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
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
   
   完成标准自定义安装并启动 Azure-SSIS IR 之后，可在存储容器的 *main.cmd.log* 文件夹中找到 *main.cmd* 的标准输出和其他执行日志。

1. 若要查看标准自定义安装的某些示例，请使用 Azure 存储资源管理器连接到我们的公共预览版容器。

   a. 在“(本地和附加)”下面，右键单击“存储帐户”，并依次选择“连接到 Azure 存储”、“使用连接字符串或共享访问签名 URI”、“下一步”。     

      ![使用共享访问签名连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. 选择“使用 SAS URI”，然后在“URI”框中输入以下 SAS URI：  

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![提供容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 依次选择“下一步”、“连接”   。

   d. 在左侧窗格中选择已连接的“publicpreview”容器，然后双击“CustomSetupScript”文件夹。   此文件夹包含以下项：

      * 一个 *Sample* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装基本任务的自定义安装程序。 该任务不会执行任何操作，而是休眠几秒。 该文件夹还包含 *gacutil* 文件夹，其整个内容（*gacutil.exe*、*gacutil.exe.config* 和 *1033\gacutlrc.dll*）都可以按原样复制到容器中。

      * 一个 *UserScenarios* 文件夹，其中包含实际用户方案中的多个自定义安装示例。

        ![公共预览版容器的内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. 双击“UserScenarios”文件夹以查找以下项： 

      * 一个 .NET FRAMEWORK 3.5  文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装自定义组件可能需要的 .NET Framework 早期版本的自定义安装程序脚本 (main.cmd  )。

      * 一个 BCP  文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 SQL Server 命令行实用程序 (MsSqlCmdLnUtils.msi  )（包括批量复制程序 (bcp  )）的自定义安装程序脚本 (main.cmd  )。

      * 一个 *EXCEL* 文件夹，其中包含用于安装 C# 程序集和库的自定义安装脚本 (*main.cmd*)，安装后可在脚本任务中使用该脚本来动态读取和写入 Azure-SSIS IR 每个节点上的 Excel 文件。 
      
        首先，请下载 [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) 和 [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)，然后将它们连同 *main.cmd* 一起上传到容器中。 另外，如果你只想使用标准的 Excel 连接管理器、Excel 源和 Excel 目标，所需的 Access Redistributable 包已预先安装在 Azure-SSIS IR 上，因此无需任何自定义安装。
      
      * 一个 *MYSQL ODBC* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 MySQL ODBC 驱动程序的自定义安装脚本 (*main.cmd*)。 通过此安装，可使用 ODBC 连接管理器、源和目标连接到 MySQL 服务器。 
     
        首先，[下载最新的 64 位和 32 位版 MySQL ODBC 驱动程序安装程序](https://dev.mysql.com/downloads/connector/odbc/)（例如 *mysql-connector-odbc-8.0.13-winx64.msi* 和 *mysql-connector-odbc-8.0.13-win32.msi*），然后将它们连同 *main.cmd* 一起上传到容器中。

      * 一个 *ORACLE ENTERPRISE* 文件夹，其中包含用于在 Azure-SSIS IR 企业版的每个节点上安装 Oracle 连接器和 OCI 驱动程序的自定义安装脚本 (*main.cmd*) 和无提示安装配置文件 (*client.rsp*)。 通过此安装程序，可使用 Oracle 连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=55179)下载适用于 Oracle 的 Microsoft 连接器 v5.0（*AttunitySSISOraAdaptersSetup.msi* 和 *AttunitySSISOraAdaptersSetup64.msi*），并从 [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 下载最新的 Oracle 客户端（例如 *winx64_12102_client.zip*），然后将它们连同 *main.cmd* 和 *client.rsp* 一起上传到容器中。 如果使用 TNS 连接到 Oracle，则还需要下载 *tnsnames.ora*，对其进行编辑，然后将其上传到容器，以便在安装期间将其复制到 Oracle 安装文件夹中。

      * 一个 *ORACLE STANDARD ADO.NET* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODP.NET 驱动程序的自定义安装脚本 (*main.cmd*)。 通过此安装程序，可使用 ADO.NET 连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，[下载最新的 Oracle ODP.NET 驱动程序](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)（例如 *ODP.NET_Managed_ODAC122cR1.zip*），然后将其连同 *main.cmd* 一起上传到容器中。
       
      * 一个 *ORACLE STANDARD ODBC* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODBC 驱动程序和配置数据源名称 (DSN) 的自定义安装脚本 (*main.cmd*)。 借助此安装程序，可以使用 ODBC 数据源类型的 ODBC 连接管理器、源和目标或 Power Query 连接管理器和源连接到 Oracle 服务器。 
      
        首先，下载最新的 Oracle Instant Client（基本包或基本精简包）和 ODBC 包，然后将它们连同 *main.cmd* 一起上传到容器中：
        * [下载 64 位包](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)（基本包：*instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*；基本精简包：*instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*；ODBC 包：*instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*） 
        * [下载 32 位包](https://www.oracle.com/technetwork/topics/winsoft-085727.html)（基本包：*instantclient-basic-nt-18.3.0.0.0dbru.zip*；基本精简包：*instantclient-basiclite-nt-18.3.0.0.0dbru.zip*；ODBC 包：*instantclient-odbc-nt-18.3.0.0.0dbru.zip*）

      * 一个 *ORACLE STANDARD OLEDB* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle OLEDB 驱动程序的自定义安装脚本 (*main.cmd*)。 通过此安装程序，可使用 OLEDB 连接管理器、源和目标连接到 Oracle 服务器。 
     
        首先，[下载最新的 Oracle OLEDB 驱动程序](https://www.oracle.com/partners/campaign/index-090165.html)（例如 *ODAC122010Xcopy_x64.zip*），然后将其连同 *main.cmd* 一起上传到容器中。

      * 一个 *POSTGRESQL ODBC* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 PostgreSQL ODBC 驱动程序的自定义安装脚本 (*main.cmd*)。 通过此安装，可使用 ODBC 连接管理器、源和目标连接到 PostgreSQL 服务器。 
     
        首先，[下载最新的 64 位和 32 位版本的 PostgreSQL ODBC 驱动程序安装程序](https://www.postgresql.org/ftp/odbc/versions/msi/)（例如 *psqlodbc_x64.msi* 和 *psqlodbc_x86.msi*），然后将它们连同 *main.cmd* 一起上传到容器中。

      * 一个 *SAP BW* 文件夹，其中包含用于在 Azure-SSIS IR 企业版的每个节点上安装 SAP .NET 连接器程序集 (*librfc32.dll*) 的自定义安装脚本 (*main.cmd*)。 通过此安装程序，可使用 SAP Business Warehouse (BW) 连接管理器、源和目标连接到 SAP BW 服务器。 
      
        首先，将 64 位或 32 位版本的 *librfc32.dll* 连同 *main.cmd* 一起从 SAP 安装文件夹上传到容器中。 然后，该脚本会在安装期间将 SAP 程序集复制到 *%windir%\SysWow64* 或 *%windir%\System32* 文件夹中。

      * 一个 STORAGE  文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Azure PowerShell 的自定义安装程序脚本 (main.cmd  )。 通过此安装程序可以部署并运行 SSIS 包，以便[运行 PowerShell 脚本来操作 Azure 存储帐户](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 
      
        将 *main.cmd*、示例 *AzurePowerShell.msi*（或使用最新版本）和 *storage.ps1* 复制到容器。 使用 *PowerShell.dtsx* 作为包的模板。 包模板中合并了 [Azure Blob 下载任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)（用于下载可修改的 PowerShell 脚本形式的 *storage.ps1*）和[执行进程任务](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)（用于在每个节点上执行脚本）。

      * 一个 *TERADATA* 文件夹，其中包含自定义安装脚本 (*main.cmd*)、其关联的文件 (*install.cmd*) 和安装程序包 ( *.msi*)。 这些文件将在 Azure-SSIS IR 企业版的每个节点上安装 Teradata 连接器、Teradata 并行传输程序 (TPT) API 和 ODBC 驱动程序。 通过此安装程序，可使用 Teradata 连接管理器、源和目标连接到 Teradata 服务器。 
      
        首先，下载 [Teradata 工具和实用工具 15.x zip 文件](http://partnerintelligence.teradata.com)（例如 *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*），然后将其连同上述 *.cmd* 和 *.msi* 文件一起上传到容器中。

      * 一个 TLS 1.2  文件夹，其中包含一个自定义安装程序脚本 (main.cmd  )，用于在 Azure-SSIS IR 的每个节点上使用强加密/更安全的网络协议 (TLS 1.2) 并禁用较旧 SSL/TLS 版本。

      * 一个 *ZULU OPENJDK* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Zulu OpenJDK 的自定义安装脚本 (*main.cmd*) 和 PowerShell 文件 (*install_openjdk.ps1*)。 借助此安装程序，可使用 Azure Data Lake Store 和 Flexible File 连接器来处理 ORC 和 Parquet 文件。 有关详细信息，请参阅 [Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 
      
        首先，[下载最新的 Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/)（例如 *zulu8.33.0.1-jdk8.0.192-win_x64.zip*），然后将其连同 *main.cmd* 和 *install_openjdk.ps1* 一起上传到容器中。

        ![用户方案文件夹中的文件夹](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 若要试用这些自定义安装示例，请将选定文件夹中的内容复制到容器中。
   
      使用数据工厂 UI 安装或重新配置 Azure-SSIS IR 时，可以在“高级设置”部分上选择“使用其他系统配置/组件安装来自定义 Azure-SSIS Integration Runtime”复选框并在“自定义安装容器 SAS URI”框中输入容器的 SAS URI。   
   
      使用 PowerShell 安装或重新配置 Azure-SSIS IR 时，请运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 并指定容器的 SAS URI 作为 `SetupScriptContainerSasUri` 参数的值。

## <a name="next-steps"></a>后续步骤

- [安装 Azure-SSIS Integration Runtime 企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [为 Azure-SSIS Integration Runtime 开发付费或许可的自定义组件](how-to-develop-azure-ssis-ir-licensed-components.md)
