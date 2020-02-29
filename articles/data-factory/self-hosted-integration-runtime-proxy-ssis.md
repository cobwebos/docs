---
title: 将自承载集成运行时配置为 SSIS 的代理
description: 了解如何将自承载集成运行时配置为 Azure-SSIS Integration Runtime 的代理。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/28/2020
ms.openlocfilehash: 701e279e349bdc3b92d5b5c2dc50e23199f9d766
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160805"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>在 Azure 数据工厂中将自承载 IR 配置为 Azure-SSIS IR 的代理

本文介绍如何在 Azure 数据工厂中使用配置为代理的自承载集成运行时（自承载 IR）的 Azure-SSIS Integration Runtime （Azure-SSIS IR）上运行 SQL Server Integration Services （SSIS）包。 

利用此功能，你可以在本地访问数据，而无需将[Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 当你的企业网络配置太复杂或策略过于严格以致于你向其中插入 Azure-SSIS IR 时，此功能很有用。

此功能将包含带有本地数据源的数据流任务的包拆分为两个过渡任务： 
* 在自承载 IR 上运行的第一个任务首先将数据从本地数据源移到 Azure Blob 存储中的临时区域。
* 第二个任务在 Azure-SSIS IR 上运行，然后将数据从临时区域移动到目标数据目标。

此功能的其他优点和功能使你能够（例如）在 Azure-SSIS IR 尚不支持的区域中设置自承载 IR，并在你的数据源防火墙上允许自承载 IR 的公共静态 IP 地址。

## <a name="prepare-the-self-hosted-ir"></a>准备自承载 IR

若要使用此功能，请先创建数据工厂，然后在其中设置 Azure-SSIS IR。 如果尚未执行此操作，请按照[设置 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)中的说明进行操作。

然后，在设置了 Azure-SSIS IR 的同一数据工厂中设置自承载 IR。 为此，请参阅[创建自承载 IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。

最后，在本地计算机或 Azure 虚拟机（VM）上下载并安装最新版本的自承载 IR 以及其他驱动程序和运行时，如下所示：
- 下载并安装最新版本的[自承载 IR](https://www.microsoft.com/download/details.aspx?id=39717)。
- 如果在包中使用对象链接和嵌入数据库（OLEDB）连接器，请在安装了自承载 IR 的同一台计算机上下载并安装相关的 OLEDB 驱动程序（如果尚未这样做）。  

  如果使用早期版本的 OLEDB 驱动程序进行 SQL Server （SQL Server Native Client [SQLNCLI.MSI]），请[下载64位版本](https://www.microsoft.com/download/details.aspx?id=50402)。  

  如果使用最新版本的 OLEDB driver for SQL Server （MSOLEDBSQL），请[下载64位版本](https://www.microsoft.com/download/details.aspx?id=56730)。  
  
  如果对其他数据库系统（如 PostgreSQL、MySQL、Oracle 等）使用 OLEDB 驱动程序，则可以从其网站下载64位版本。
- 如果尚未执行此操作，请在安装了自承载 IR 的同一台计算机上[下载并安装64位版本的 Visual C++ （VC）运行时](https://www.microsoft.com/download/details.aspx?id=40784)。

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>为暂存准备 Azure Blob 存储链接服务

如果尚未执行此操作，请在设置了 Azure-SSIS IR 的同一数据工厂中创建一个 Azure Blob 存储链接服务。 为此，请参阅[创建 Azure 数据工厂链接服务](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)。 请确保执行以下操作：
- 对于 "**数据存储**"，请选择 " **Azure Blob 存储**"。  
- 对于 "**通过集成运行时连接**"，请选择**AutoResolveIntegrationRuntime**。  
- 对于**身份验证方法**，请选择**帐户密钥**、 **SAS URI**或**服务主体**。  

    >[!TIP]
    >如果选择 " **服务主体**"，请至少授予 *存储 Blob 数据参与者* 角色。 有关详细信息，请参阅 [Azure Blob 存储连接器](connector-azure-blob-storage.md#linked-service-properties)。

![为暂存准备 Azure Blob 存储链接服务](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>使用自承载 IR 作为代理来配置 Azure-SSIS IR

准备好用于过渡的自承载 IR 和 Azure Blob 存储链接服务之后，现在可以使用自承载 IR 将新的或现有的 Azure-SSIS IR 配置为数据工厂门户或应用程序中的代理。 但在执行此操作之前，如果现有 Azure-SSIS IR 已在运行，请将其停止，然后重新启动它。

1. 在 "**集成运行时安装**" 窗格中，选择 "**下一步**" 跳过 "**常规设置**" 和 " **SQL 设置**" 部分。 

1. 在 "**高级设置**" 部分中，执行以下操作：

   1. 选中 "**将自承载 Integration Runtime 设置为 Azure-SSIS Integration Runtime 的代理**" 复选框。 

   1. 在 "**自承载 Integration Runtime** " 下拉列表中，选择现有的自承载 IR 作为 Azure-SSIS IR 的代理。

   1. 在 "**暂存存储链接服务**" 下拉列表中，选择现有的 Azure Blob 存储链接服务，或创建一个新的临时服务。

   1. 在 "**过渡路径**" 框中，指定所选 Azure blob 存储帐户中的 blob 容器，或将其留空以使用默认值进行暂存。

   1. 选择“继续”。

   ![使用自承载 IR 的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

你还可以使用 PowerShell 将新的或现有 Azure-SSIS IR 与自承载 IR 一起配置为代理。

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>允许 SSIS 包通过代理进行连接

通过将最新的 SSDT 与用于 Visual Studio 的 SSIS 项目扩展或独立安装程序结合使用，可找到已在 OLEDB 或平面文件连接管理器中添加的新的 `ConnectByProxy` 属性。
* [下载适用于 Visual Studio 的 SSDT with SSIS 项目扩展](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [下载独立安装程序](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

当您使用 OLEDB 或平面文件源设计包含数据流任务的新包时，可以访问本地数据库或文件时，可以通过在相关连接管理器的 "**属性**" 窗格中将其设置为*True*来启用此属性。

![启用 ConnectByProxy 属性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

你还可以在运行现有包时启用此属性，而无需逐个手动更改。  存在两个选项：
- **选项 A**：打开、重新生成并重新部署包含这些包的项目，其中包含要在 Azure-SSIS IR 上运行的最新 SSDT。 然后，可以通过将相应的连接管理器的属性设置为*True*来启用属性。 当他们从 SSMS 运行包时，这些连接管理器将出现在 "**执行包**" 弹出窗口的 "**连接管理器**" 选项卡上。

  ![Enable ConnectByProxy property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  还可以通过在 "[执行 SSIS 包" 活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)的 "**连接管理**器" 选项卡上显示的相关连接管理器将其设置为*True*来启用属性，以便在数据工厂管道中运行包时。
  
  ![Enable ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **选项 B：** 重新部署包含这些包的项目，以便在 SSIS IR 上运行。 然后，你可以通过提供其属性路径来启用属性，`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，并将其设置为 True，当你从 SSMS 中运行包时，会在 "**执行包**" 弹出窗口的 "**高级**" 选项卡上将其设置为*True* 。

  ![Enable ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  您还可以通过在数据工厂管道中运行包时，提供属性路径并 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，并将其设置为*True* ，以作为 "[执行 SSIS 包" 活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)的 "**属性替代**" 选项卡上的属性重写，来启用属性。
  
  ![Enable ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>调试第一个和第二个临时任务

在自承载 IR 上，可以在*C:\ProgramData\SSISTelemetry*文件夹中找到运行时日志，并在*C:\ProgramData\SSISTelemetry\ExecutionLog*文件夹中找到第一次暂存任务的执行日志。  你可以在 SSISDB 或指定的日志记录路径中找到第二个过渡任务的执行日志，具体取决于你是将包存储在 SSISDB 还是文件系统、文件共享或 Azure 文件中。 还可以在第二个临时任务的执行日志中找到第一个暂存任务的唯一 Id。 

![第一个暂存任务的唯一 ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>在临时任务中使用 Windows 身份验证

如果自承载 IR 上的暂存任务需要 Windows 身份验证，请[将 SSIS 包配置为使用相同的 windows 身份验证](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 

将使用自承载的 IR 服务帐户（默认情况下为*NT SERVICE\DIAHostService*）调用你的暂存任务，你的数据存储将使用 Windows 身份验证帐户进行访问。 这两个帐户都需要为其分配特定安全策略。 在自承载 IR 计算机上，" > **本地策略**" > **用户权限分配**"中转到"**本地安全策略**"，然后执行以下操作：

1. 分配 "*调整进程的内存配额*"，并将*进程级别令牌策略替换*为自承载的 IR 服务帐户。 当你安装具有默认服务帐户的自承载 IR 时，会自动发生这种情况。 如果使用不同的服务帐户，则为其分配相同的策略。

1. 将 "*作为服务登录*" 策略分配给 Windows 身份验证帐户。

## <a name="billing-for-the-first-and-second-staging-tasks"></a>第一和第二个过渡任务的计费

在自承载 IR 上运行的第一个暂存任务将单独计费，就像在自承载 IR 上运行的任何数据移动活动计费一样。 这是在[Azure 数据工厂数据管道定价](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)一文中指定的。

不会单独对 Azure-SSIS IR 上运行的第二个暂存任务计费，但正在运行的 Azure-SSIS IR 按[Azure-SSIS IR 定价](https://azure.microsoft.com/pricing/details/data-factory/ssis/)文章中指定的方式计费。

## <a name="current-limitations"></a>当前限制

- 目前仅支持具有开放式数据库连接（ODBC）、OLEDB 或平面文件连接管理器以及 ODBC、OLEDB 或平面文件源的数据流任务。 
- 目前仅支持使用*帐户密钥*、*共享访问签名（SAS） URI*或*服务主体*身份验证配置的 Azure Blob 存储链接服务。

## <a name="next-steps"></a>后续步骤

将自承载 IR 配置为你的 Azure-SSIS IR 的代理后，你可以部署和运行你的包，以便在数据工厂管道中作为执行 SSIS 包活动访问本地数据。 若要了解如何操作，请参阅[在数据工厂管道中运行 ssis 包作为执行 Ssis 包活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
