---
title: 自定义 Azure-SSIS 集成运行时的设置
description: 本文介绍如何使用 Azure-SSIS 集成运行时的自定义设置接口来安装其他组件或更改设置
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
ms.openlocfilehash: 3aabf68b6763e5815a03021cb02683d509c26190
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415051"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>自定义 Azure-SSIS 集成运行时的设置

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure-SQL 服务器集成服务集成运行时 （Azure-SSIS IR） 的自定义设置提供了一个接口，用于在 Azure-SSIS IR 的设置或重新配置期间添加您自己的步骤。 

通过使用自定义设置，可以更改默认的操作配置或环境，例如，启动其他 Windows 服务、保留文件共享的访问凭据或使用强加密/更安全的网络协议 （TLS 1.2）。 或者，您可以在 Azure-SSIS IR 的每个节点上安装其他组件，如程序集、驱动程序或扩展。

您可以通过两种方式在 Azure-SSIS IR 上执行自定义设置： 
* **无需脚本即可快速设置**：运行一些常见的系统配置和 Windows 命令，或者安装一些常用或建议的其他组件，而无需使用任何脚本。
* **使用脚本的标准自定义设置**：准备脚本及其关联文件，并将它们一起上载到 Azure 存储帐户中的 Blob 容器。 然后，在设置或重新配置 Azure-SSIS IR 时，为容器提供共享访问签名 （SAS） 统一资源标识符 （URI）。 然后，Azure-SSIS IR 的每个节点都会从容器下载脚本及其关联文件，并运行具有提升权限的自定义设置。 自定义设置完成后，每个节点都会将执行和其他日志的标准输出上载到容器。

您可以安装免费、未经许可的组件和付费许可组件，并安装快速和标准的自定义设置。 如果您是独立的软件供应商 （ISV），请参阅[为 Azure-SSIS IR 开发付费或许可组件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> 为了从将来的增强功能中获益，我们建议使用 v3 或更高版本的一系列节点来设置 Azure-SSIS IR。

## <a name="current-limitations"></a>当前限制

以下限制仅适用于标准自定义设置：

- 如果要在脚本中使用*gacutil.exe*在全局程序集缓存 （GAC） 中安装程序集，则需要在自定义设置中提供*gacutil.exe。* 或者，您可以使用我们的 *"公共预览"* 容器中提供的副本，稍后在"说明"部分中讨论。

- 如果要引用脚本中的子文件夹 *，msiexec.exe*不支持引用根文件夹`.\`的表示法。 使用 命令（如`msiexec /i "MySubfolder\MyInstallerx64.msi" ...`而不是`msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

- Azure-SSIS IR 当前不支持由 Windows 自动创建的管理共享或隐藏的网络共享。

- Azure-SSIS IR 不支持 IBM iSeries 访问 ODBC 驱动程序。 在自定义设置期间，您可能会看到安装错误。 如果是，请联系 IBM 支持人员寻求帮助。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要自定义 Azure-SSIS IR，您需要以下项目：

- [Azure 订阅](https://azure.microsoft.com/)

- [预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 对于快速自定义安装并非必需。 对于标准自定义安装，请在 Blob 容器中上传并存储自定义安装脚本及其关联文件。 自定义安装进程还会将其执行日志上传到相同的 Blob 容器。

## <a name="instructions"></a>Instructions

1. 如果要使用 PowerShell 设置或重新配置 Azure-SSIS IR，请下载并安装[Azure PowerShell](/powershell/azure/install-az-ps)。 对于快速自定义安装，请跳到步骤 4。

1. 准备自定义安装脚本及其关联的文件（例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 文件）。

   * 您必须有一个名为*main.cmd*的脚本文件，这是自定义设置的入口点。  
   * 为了确保脚本可以静默执行，我们建议您先在本地计算机上测试它。  
   * 如果希望将其他工具（例如*msiexec.exe*）生成的其他日志上载到容器，请将预定义的环境变量`CUSTOM_SETUP_SCRIPT_LOG_DIR`指定 为 脚本中的日志文件夹（例如 *，msiexec /i xxx.msi/quiet/lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log）。*

1. 下载、安装和打开[Azure 存储资源管理器](https://storageexplorer.com/)。 为此，请执行以下操作：

   a. 在 **"本地"和"附加"** 下，右键单击 **"存储帐户**"，然后选择"**连接到 Azure 存储**"。

      ![连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 依次选择“使用存储帐户名和密钥”、“下一步”。********

      ![使用存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. 输入 Azure 存储帐户名称和密钥，选择 **"下一步**"，然后选择"**连接**"。

      ![提供存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 在连接的 Azure 存储帐户下，右键单击**Blob 容器**，选择 **"创建 Blob 容器**"，然后命名新容器。

      ![创建 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 选择新容器，然后上载自定义设置脚本及其关联文件。 请确保在容器的顶层上载*main.cmd，* 而不是在任何文件夹中。 还要确保容器仅包含必要的自定义设置文件，以便以后将它们下载到 Azure-SSIS IR 不会花很长时间。 自定义设置的最大持续时间当前设置为超时前 45 分钟。这包括从容器下载所有文件并将其安装在 Azure-SSIS IR 上的时间。 如果设置需要更多时间，则提高支持票证。

      ![将文件上传到 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 右键单击容器，然后选择 **"获取共享访问签名**"。

      ![获取容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 使用足够长的过期时间和读取/写入/列表权限为容器创建 SAS URI。 每当重新映像或重新启动 Azure-SSIS IR 的任何节点时，都需要 SAS URI 下载并运行自定义设置脚本及其关联文件。 需要拥有写入权限才能上传安装执行日志。

      > [!IMPORTANT]
      > 确保 SAS URI 不会过期，并且自定义设置资源始终在 Azure-SSIS IR 的整个生命周期中可用，从创建到删除，尤其是在在此期间定期停止并启动 Azure-SSIS IR 时。

      ![生成容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 复制并保存容器的 SAS URI。

      ![复制并保存共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 使用数据出厂 UI 设置或重新配置 Azure-SSIS IR 时，可以通过在 **"集成运行时设置****"窗格的高级设置**部分中选择 **"自定义 Azure-SSIS 集成运行时"复选框**来添加或删除自定义设置。 

   如果要添加标准自定义设置，请在 **"自定义设置容器 SAS URI"** 框中输入容器的 SAS URI。 
   
   如果要添加快速自定义设置，请选择 **"新建"** 以打开"**添加快速自定义设置**窗格"，然后在 **"快速自定义设置类型**"下拉列表中选择一种类型：

   * 如果选择**Run cmdkey 命令**类型，则可以通过在 **/Add、/User**和 **/Pass**框中输入目标计算机名称或域名、帐户名称或用户名以及帐户密钥或密码，在 Azure-SSIS IR 上保留 **/User**文件共享或 Azure 文件共享的访问凭据。 这类似于在本地计算机上运行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey)命令。
   
   * 如果选择 **"添加环境变量**类型"，则可以通过在**变量名称**和**变量值**框中输入环境变量名称和值，添加 Windows 环境变量，以在 Azure-SSIS IR 上运行的包中使用。 这类似于在本地计算机上运行 Windows[集](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1)命令。

   * 如果选择 **"安装许可组件**类型"，则可以在 **"组件名称**下拉列表"中从我们的 ISV 合作伙伴中选择集成组件：

     * 如果选择**SentryOne 的任务工厂**组件，则可以通过在**许可证密钥**框中输入从 SentryOne 购买的产品许可证密钥，在 Azure-SSIS IR 上安装 SentryOne 中[的任务工厂](https://www.sentryone.com/products/task-factory/high-performance-ssis-components)组件套件。 当前的集成版本是 2019.4.3****。

     * 如果选择“oh22 的 HEDDA.IO”组件，可在购买 oh22 服务后，在 Azure-SSIS IR 上安装 oh22 的 [HEDDA.IO](https://hedda.io/ssis-component/) 数据质量/清理组件****。 当前的集成版本是 1.0.13****。

     * 如果选择**oh22 的SQLPhonetics.NET**组件，则可以通过在 **"许可证"密钥**框中输入从它们购买的产品许可证密钥，在 Azure-SSIS IR 上安装[SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/)数据质量/匹配组件。 当前的集成版本是 1.0.43****。

     * 如果选择**KingswaySoft 的 SSIS 集成工具包组件**，则可以通过在 Azure-SSIS IR 上输入从它们购买的产品许可证密钥，在 Azure-SSIS IR 上输入从它们购买的产品许可证密钥，从 KingswaySoft 安装用于 CRM/ERP/营销/协作应用的[SSIS 集成工具包](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365)套件，例如**License key**Microsoft 动态/SharePoint/项目服务器、Oracle/Salesforce 营销云等。 目前的集成版本是**2019.2。**

     * 如果选择**KingswaySoft 的 SSIS 生产力包**组件，则可以通过在**许可证密钥**框中输入从它们购买的产品许可证密钥，在 Azure-SSIS IR 上安装来自金斯威软件的[SSIS 生产力包](https://www.kingswaysoft.com/products/ssis-productivity-pack)组件套件。 当前集成版本为**10.0**。

     * 如果选择**Theobald 软件的 Xtract IS**组件，则可以通过拖动从 Azure-SSIS IR 上从 Theobald 软件安装 SAP 系统的[Xtract IS](https://theobald-software.com/en/xtract-is/)连接器套件（ERP、S/4HANA、BW），从而将从它们购买的产品许可证文件拖放到**许可证文件**框中&。 当前集成版本为**6.1.1.3**。

   您添加的快速自定义设置将显示在 **"高级设置"部分中**。 要删除它们，请选择其复选框，然后选择"**删除**"。

   ![包含自定义安装的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. 使用 PowerShell 设置或重新配置 Azure-SSIS IR 时，可以通过在启动 Azure-SSIS `Set-AzDataFactoryV2IntegrationRuntime` IR 之前运行 cmdlet 来添加或删除自定义设置。
   
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
   
   在标准自定义设置完成并启动 Azure-SSIS IR 后，您可以在存储容器的主 *.cmd.log*文件夹中找到*main.cmd*和其他执行日志的标准输出。

1. 要查看标准自定义设置的一些示例，请使用 Azure 存储资源管理器连接到我们的公共预览容器。

   a. 在“(本地和附加)”下面，右键单击“存储帐户”，并依次选择“连接到 Azure 存储”、“使用连接字符串或共享访问签名 URI”、“下一步”。********************

      ![使用共享访问签名连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. 选择 **"使用 SAS URI"，** 然后在**URI**框中输入以下 SAS URI：

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![提供容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 依次选择“下一步”、“连接”********。

   d. 在左侧窗格中，选择已连接**的公共预览**容器，然后双击*自定义设置脚本*文件夹。 此文件夹包含以下项：

      * *示例*文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装基本任务的自定义设置。 该任务不会执行任何操作，而是休眠几秒。 该文件夹还包含一个*gacutil*文件夹，其全部内容（gacutil.exe、gacutil.exe.config 和*1033_gacutlrc.dll）* 可以像一样复制到您的容器。*gacutil.exe* *gacutil.exe.config*

      * *UserScenarios*文件夹，其中包含来自真实用户方案的多个自定义设置示例。

        ![公共预览版容器的内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. 双击 *"用户方案"* 文件夹可查找以下项目：

      * *.NET FRAMEWORK 3.5*文件夹，其中包含自定义设置脚本 *（main.cmd），* 用于安装 Azure-SSIS IR 的每个节点上的自定义组件可能需要的早期版本.NET Framework。

      * *BCP*文件夹，其中包含一个自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 的每个节点上安装 SQL Server 命令行实用程序 *（MsSqlCmdLnUtils.msi），* 包括批量复制程序 *（bcp*）。

      * *EXCEL*文件夹，其中包含用于安装 C# 程序集和库的自定义设置脚本 *（main.cmd），* 可用于在脚本任务中动态读取和写入 Azure-SSIS IR 的每个节点上的 Excel 文件。 
      
        首先，下载[*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/)和[*DocumentFormat.OpenXml.dll，*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)然后将它们连同*main.cmd*一起上载到您的容器。 或者，如果只想使用标准 Excel 连接管理器、Excel 源和 Excel 目标，则所需的可再分发访问已在 Azure-SSIS IR 上预装，因此不需要任何自定义设置。
      
      * *MYSQL ODBC*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 的每个节点上安装 MySQL ODBC 驱动程序。 此设置允许您使用 ODBC 连接管理器、源和目标连接到 MySQL 服务器。 
     
        首先，[下载 MySQL ODBC 驱动程序安装程序的最新 64 位和 32 位版本](https://dev.mysql.com/downloads/connector/odbc/)（例如 *，mysql 连接器-odbc-8.0.13-winx64.msi*和*mysql 连接器-odbc-8.0.13-win32.msi），* 然后将它们与*main.cmd*一起上载到您的容器。

      * *ORACLE ENTERPRISE*文件夹，其中包含自定义设置脚本 *（main.cmd*） 和静默安装配置文件 *（client.rsp），* 用于在 Azure-SSIS IR 企业版的每个节点上安装 Oracle 连接器和 OCI 驱动程序。 此设置允许您使用 Oracle 连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，从[微软下载中心和](https://www.microsoft.com/en-us/download/details.aspx?id=55179)最新的 Oracle 客户端（例如*winx64_12102_client.zip）* 下载 Oracle 的 Microsoft 连接器 v5.0（AtunitySISOraAdaptersSetup.msi 和*AtunitySISOraAdaptersSetup64.msi），* 然后将它们连同*main.cmd*和*client.rsp*一起上传到您的容器。*AttunitySSISOraAdaptersSetup.msi* [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 如果使用 TNS 连接到 Oracle，则还需要下载*tnsnames.ora，* 对其进行编辑，并将其上载到容器，以便在安装期间将其复制到 Oracle 安装文件夹。

      * *ORACLE 标准ADO.NET*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODP.NET驱动程序。 此设置允许您使用ADO.NET连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，[下载最新的 Oracle ODP.NET驱动程序](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)（例如*ODP.NET_Managed_ODAC122cR1.zip），* 然后将它与*main.cmd*一起上载到您的容器。
       
      * *ORACLE 标准 ODBC*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODBC 驱动程序并配置数据源名称 （DSN）。 此设置允许您使用 ODBC 连接管理器、源和目标或电源查询连接管理器以及具有 ODBC 数据源类型的源连接到 Oracle 服务器。 
      
        首先，下载最新的 Oracle 即时客户端（基本软件包或基本精简版包）和 ODBC 软件包，然后将它们连同*main.cmd*一起上载到您的容器：
        * [下载 64 位包](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)（基本包：*即时客户端-基本窗口.x64-18.3.0.0dbru.zip;* 基本精简版封装：*即时客户端-基本精简窗口.x64-18.3.0.0dbru.zip;* ODBC 封装：*即时客户端-odbc-windows.x64-18.3.0.0dbru.zip*） 
        * [下载 32 位包](https://www.oracle.com/technetwork/topics/winsoft-085727.html)（基本包：*即时客户端-基本 nt-18.3.0.0dbru.zip;* 基本精简包：*即时客户端-基本精简-nt-nt-18.3.0.0dbru.zip;* ODBC 封装：*即时客户端-odbc-nt-18.3.0.0dbru.zip*）

      * *ORACLE 标准 OLEDB*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 的每个节点上安装 Oracle OLEDB 驱动程序。 此设置允许您使用 OLEDB 连接管理器、源和目标连接到 Oracle 服务器。 
     
        首先，[下载最新的 Oracle OLEDB 驱动程序](https://www.oracle.com/partners/campaign/index-090165.html)（例如 *，ODAC122010Xcopy_x64.zip），* 然后将其与*main.cmd*一起上载到容器。

      * *POSTGRESQL ODBC*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 的每个节点上安装 PostgreSQL ODBC 驱动程序。 此设置允许您使用 ODBC 连接管理器、源和目标连接到 PostgreSQL 服务器。 
     
        首先，[下载最新的 64 位和 32 位版本的 PostgreSQL ODBC 驱动程序安装程序](https://www.postgresql.org/ftp/odbc/versions/msi/)（例如 *，psqlodbc_x64.msi*和*psqlodbc_x86.msi），* 然后将它们连同*main.cmd*一起上载到您的容器。

      * *SAP BW*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 企业版的每个节点上安装 SAP .NET 连接器程序集 *（librfc32.dll）。* 此设置允许您使用 SAP 业务仓库 （BW） 连接管理器、源和目标连接到 SAP BW 服务器。 
      
        首先，将 64 位或 32 位版本的*librfc32.dll*从 SAP 安装文件夹中连同*main.cmd*一起上载到容器。 然后，脚本在设置期间将 SAP 程序集复制到 *%windir%_SysWow64*或 *%windir%_System32*文件夹。

      * *存储*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于在 Azure-SSIS IR 的每个节点上安装 Azure PowerShell。 此设置允许您部署和运行运行 PowerShell 脚本的 SSIS 包[来操作 Azure 存储帐户](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 
      
        将*main.cmd*、*示例 AzurePowerShell.msi（* 或使用最新版本）和*存储.ps1*复制到容器。 使用*PowerShell.dtsx*作为包的模板。 包模板结合了[Azure Blob 下载任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)，该任务将*存储.ps1*下载为可修改的 PowerShell 脚本，另一个[程序任务 （执行进程任务](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)）在每个节点上执行脚本。

      * *一个TERADATA*文件夹，其中包含一个自定义设置脚本 *（main.cmd*），其关联的文件 *（install.cmd*） 和安装程序包 *（.msi*）。 这些文件在 Azure-SSIS IR 企业版的每个节点上安装 Teradata 连接器、Teradata 并行传输器 （TPT） API 和 ODBC 驱动程序。 此设置允许您使用 Teradata 连接管理器、源和目标连接到 Teradata 服务器。 
      
        首先，[下载 Teradata 工具和实用程序 15.x zip 文件](http://partnerintelligence.teradata.com)（例如 *，TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip），* 然后将它与前面提到的 *.cmd*和 *.msi*文件一起上载到容器。

      * *TLS 1.2*文件夹，其中包含自定义设置脚本 *（main.cmd*），用于使用强加密/更安全的网络协议 （TLS 1.2），并在 Azure-SSIS IR 的每个节点上禁用较旧的 SSL/TLS 版本。

      * *ZULU OPENJDK*文件夹，其中包含自定义设置脚本 *（main.cmd*） 和 PowerShell 文件 *（install_openjdk.ps1），* 用于在 Azure-SSIS IR 的每个节点上安装祖鲁 OpenJDK。 此设置允许您使用 Azure 数据湖存储和灵活文件连接器来处理 ORC 和 Parquet 文件。 有关详细信息，请参阅[集成服务的 Azure 功能包](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 
      
        首先，[下载最新的祖鲁开放JDK（](https://www.azul.com/downloads/zulu/zulu-windows/)例如 *，zulu8.33.0.1-jdk8.0.192-win_x64.zip），* 然后连同*main.cmd*和*install_openjdk.ps1*一起上传到您的容器。

        ![用户方案文件夹中的文件夹](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 要尝试这些自定义设置示例，请将内容从所选文件夹复制到容器。
   
      使用数据出厂 UI 设置或重新配置 Azure-SSIS IR 时，请在"高级设置"部分选中"**自定义 Azure-SSIS 集成运行时"** 复选框，然后在 **"高级设置"** 部分输入容器的 SAS URI。 **Custom setup container SAS URI**
   
      使用 PowerShell 设置或重新配置 Azure-SSIS IR 时，`Set-AzDataFactoryV2IntegrationRuntime`使用容器的 SAS URI 运行 cmdlet`SetupScriptContainerSasUri`作为参数值。

## <a name="next-steps"></a>后续步骤

- [设置 Azure-SSIS 集成运行时的企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [为 Azure-SSIS 集成运行时开发付费或许可的自定义组件](how-to-develop-azure-ssis-ir-licensed-components.md)
