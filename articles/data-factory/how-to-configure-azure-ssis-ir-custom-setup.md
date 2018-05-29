---
title: Azure-SSIS 集成运行时的自定义安装 | Microsoft Docs
description: 本文介绍如何使用 Azure-SSIS 集成运行时的自定义安装界面
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: ff47060ddfee458279c9fed0fd3fcafcf35229d2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885432"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 集成运行时的自定义安装

使用 Azure-SSIS 集成运行时的自定义安装界面可以更改默认的操作配置或环境（例如，启动其他 Windows 服务），或者在 Azure-SSIS IR 的每个节点上安装其他组件（例如程序集、驱动程序或扩展）。 一般情况下，该运行时会提供一个界面，用于在预配或重新配置 Azure-SSIS IR 期间添加你自己的安装步骤。

若要配置自定义安装，需准备一个脚本及其关联的文件，并将其上传到 Azure 存储帐户中的 Blob 容器。 预配或重新配置 Azure-SSIS IR 时，为容器提供共享访问签名 (SAS) 统一资源标识符 (URI)。 然后，Azure-SSIS IR 的每个节点将从该容器下载该脚本及其关联的文件，并使用提升的特权运行自定义安装。 自定义安装完成后，每个节点会将标准执行输出和其他日志上传到容器中。

既可以安装免费或未许可的组件，也可以安装付费或许可的组件。 如果你是 ISV，请参阅[如何为 Azure-SSIS IR 开发付费或许可的组件](how-to-develop-azure-ssis-ir-licensed-components.md)。


## <a name="current-limitations"></a>当前限制

-   如果想要使用 `gacutil.exe` 将程序集安装到全局程序集缓存 (GAC) 中，则需要在自定义安装过程中提供该工具，或使用公共预览版容器中提供的副本。

-   如果需要使用自定义安装程序将 Azure-SSIS IR 加入 VNet，仅支持 Azure 资源管理器 VNet。 不支持经典 VNet。

-   Azure-SSIS IR 目前不支持管理共享。

-   如果想要在自定义设置中将文件共享映射到驱动器，则 `net use` 命令当前不受支持。 因此，不能使用像 `net use d: \\fileshareserver\sharename` 这样的命令。 请改用 `cmdkey` 命令（例如，`cmdkey /add:fileshareserver /user:yyy /pass:zzz`）直接在包中访问 `\\fileshareserver\folder`。

## <a name="prerequisites"></a>先决条件

若要自定义 Azure-SSIS IR，需要做好以下准备：

-   [Azure 订阅](https://azure.microsoft.com/)

-   [Azure SQL 数据库或托管实例服务器](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 若要进行自定义安装，请在 Blob 容器中上传并存储自定义安装脚本及其关联文件。 自定义安装进程还会将其执行日志上传到相同的 Blob 容器。

## <a name="instructions"></a>说明

2.  下载并安装 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)（5.4 或更高版本）。

3.  准备自定义安装脚本及其关联的文件（例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 文件）。

    1.  必须创建一个名为 `main.cmd` 的脚本文件，即自定义安装程序的入口点。

    2.  如果想要将其他工具（例如 `msiexec.exe`）生成的其他日志上传到容器，请在脚本中指定预定义的环境变量 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 作为日志文件夹（例如 `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`）。

4.  下载、安装并启动 [Azure 存储资源管理器](http://storageexplorer.com/)。

    1.  在“(本地和附加)”下面，右键单击“存储帐户”并选择“连接到 Azure 存储”。

       ![连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  选择“使用存储帐户名和密钥”并选择“下一步”。

       ![使用存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  输入 Azure 存储帐户名和密钥，并依次选择“下一步”、“连接”。

       ![提供存储帐户名和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  在已连接的 Azure 存储帐户下面，右键单击“Blob 容器”，选择“创建 Blob 容器”，并为新容器命名。

       ![创建 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  选择新容器并上传自定义安装脚本及其关联的文件。 请务必将 `main.cmd` 上传到容器的顶级目录，而不要上传到任何文件夹中。 

       ![将文件上传到 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  右键单击容器，并选择“获取共享访问签名”。

       ![获取容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  为容器创建一个过期时间足够长且具有读取 + 写入 + 列出权限的 SAS URI。 每当重置 Azure-SSIS IR 的任何节点的映像时，都需要使用该 SAS URI 来下载并运行自定义安装脚本及其关联文件。 需要拥有写入权限才能上传安装执行日志。

       ![生成容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  复制并保存容器的 SAS URI。

       ![复制并保存共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  使用 PowerShell 预配或重新配置 Azure-SSIS IR 时，在启动 Azure-SSIS IR 之前，请运行 `Set-AzureRmDataFactoryV2IntegrationRuntime` cmdlet 并指定容器的 SAS URI 作为新 `SetupScriptContainerSasUri` 参数的值。 例如：

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  完成自定义安装并启动 Azure-SSIS IR 之后，可在存储容器的 `main.cmd.log` 文件夹中找到 `main.cmd` 的标准输出和其他执行日志。

2.  若要查看其他自定义安装示例，请使用 Azure 存储资源管理器连接到公共预览版容器。

    a.  在“(本地和附加)”下面，右键单击“存储帐户”，并依次选择“连接到 Azure 存储”、“使用连接字符串或共享访问签名 URI”、“下一步”。

       ![使用共享访问签名连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  选择“使用 SAS URI”并输入公共预览版容器的以下 SAS URI。 依次选择“下一步”、“连接”。

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![提供容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. 选择已连接的公共预览版容器，并双击 `CustomSetupScript` 文件夹。 此文件夹包含以下项：

       1. 一个 `Sample` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装基本任务的自定义安装程序。 该任务不会执行任何操作，而是休眠几秒。 该文件夹还包含 `gacutil` 文件夹，其中包含 `gacutil.exe`。

       2. 一个 `UserScenarios` 文件夹，其中包含用于实际用户方案的多个自定义设置。

    ![公共预览版容器的内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. 双击 `UserScenarios` 文件夹。 此文件夹包含以下项：

       1. 一个 `.NET FRAMEWORK 3.5` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装自定义组件可能需要的 .NET Framework 早期版本的自定义安装程序。

       2. 一个 `BCP` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 SQL Server 命令行实用工具 (`MsSqlCmdLnUtils.msi`)（包括批量复制程序 (`bcp`)）的自定义安装程序。

       3. 一个 `EXCEL` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装开源程序集（`DocumentFormat.OpenXml.dll`、`ExcelDataReader.DataSet.dll` 和 `ExcelDataReader.dll`）的自定义安装程序。

       4. 一个 `MSDTC` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上修改 Microsoft 分布式事务处理协调器 (MSDTC) 实例的网络和安全配置的自定义安装。

       5. 一个 `ORACLE ENTERPRISE` 文件夹，其中包含用于在 Azure-SSIS IR 企业版的每个节点上安装 Oracle OCI 驱动程序的自定义安装脚本 (`main.cmd`) 和无提示安装配置文件 (`client.rsp`)。 此安装程序允许使用 Oracle 连接管理器、源和目标。 首先，从 [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 下载最新的 Oracle 客户端（例如 `winx64_12102_client.zip`），然后将其与 `main.cmd` 和 `client.rsp` 一起上传到容器中。 如果使用 TNS 连接到 Oracle，则还需要下载 `tnsnames.ora`，对其进行编辑，然后将其上传到容器，以便在安装期间将其复制到 Oracle 安装文件夹中。

       6. 一个 `ORACLE STANDARD` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODP.NET 驱动程序的自定义安装脚本 (`main.cmd`)。 此安装程序允许使用 ADO.NET 连接管理器、源和目标。 首先，从 [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) 下载最新的 Oracle ODP.NET 驱动程序（例如 `ODP.NET_Managed_ODAC122cR1.zip`），然后将其与 `main.cmd` 一起上传到容器中。

       7. 一个 `SAP BW` 文件夹，其中包含用于在 Azure-SSIS IR 企业版的每个节点上安装 SAP .NET 连接器程序集 (`librfc32.dll`) 的自定义安装脚本 (`main.cmd`)。 此安装程序允许使用 SAP BW 连接管理器、源和目标。 首先，将 64 位或 32 位版本的 `librfc32.dll` 连同 `main.cmd` 一起从 SAP 安装文件夹上传到容器中。 然后，该脚本会在安装期间将 SAP 程序集复制到 `%windir%\SysWow64` 或 `%windir%\System32` 文件夹中。

       8. 一个 `STORAGE` 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Azure PowerShell 的自定义安装程序。 此安装程序允许部署并运行 SSIS 包，以便运行 [PowerShell 脚本来操作 Azure 存储帐户](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 将 `main.cmd`、示例 `AzurePowerShell.msi`（或安装最新版本）和 `storage.ps1` 复制到容器。 使用 PowerShell.dtsx 作为包的模板。 包模板中合并了 [Azure Blob 下载任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)（包括可修改 PowerShell 脚本形式的 `storage.ps1`）和[执行进程任务](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)（在每个节点上执行脚本）。

       9. 一个 `TERADATA` 文件夹，其中包含自定义安装脚本 (`main.cmd)`)、其关联的文件 (`install.cmd`) 和安装程序包 (`.msi`)。 这些文件将在 Azure-SSIS IR 企业版的每个节点上安装 Teradata 连接器、TPT API 和 ODBC 驱动程序。 此安装程序允许使用 Teradata 连接管理器、源和目标。 首先，从 [Teradata](http://partnerintelligence.teradata.com) 下载 Teradata 工具和实用工具 (TTU) 15.x zip 文件（例如 `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`），然后将其连同上述 `.cmd` 和 `.msi` 文件一起上传到容器中。

    ![用户方案文件夹中的文件夹](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. 若要试用这些自定义安装示例，请将选定文件夹中的内容复制并粘贴到容器中。 使用 PowerShell 预配或重新配置 Azure-SSIS IR 时，请运行 `Set-AzureRmDataFactoryV2IntegrationRuntime` cmdlet 并指定容器的 SAS URI 作为新 `SetupScriptContainerSasUri` 参数的值。

## <a name="next-steps"></a>后续步骤

-   [Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [如何为 Azure-SSIS 集成运行时开发付费或许可的自定义组件](how-to-develop-azure-ssis-ir-licensed-components.md)