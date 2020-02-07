---
title: 将自承载集成运行时配置为 SSIS 的代理
description: 了解如何配置自承载 Integration Runtime 作为 Azure-SSIS Integration Runtime 的代理。
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
ms.date: 02/06/2020
ms.openlocfilehash: b20a615691d95c04574e2909f69b5a83a97f9d14
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048951"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>在 ADF 中将自承载 IR 配置为 Azure-SSIS IR 的代理

本文介绍如何在 Azure 数据工厂（ADF）中使用配置为代理的自承载 IR 在 Azure-SSIS Integration Runtime （IR）上运行 SQL Server Integration Services （SSIS）包。  此功能允许你在本地访问数据而无需将[Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。  如果你的公司网络的配置/限制策略过于复杂，则可以在其中插入 Azure-SSIS IR。

此功能会将包含包含本地数据源的数据流任务的包拆分为两个过渡任务：第一个在自承载 IR 上运行的第一个任务是将数据从本地数据源移到 Azure Blob 存储中的临时区域，同时然后，在您的 Azure-SSIS IR 上运行的第二个将数据从临时区域移到预期的数据目标。

此功能还提供了其他优点/功能，因为它允许你预配 Azure-SSIS IR 尚不支持的区域中的自承载 IR，允许在数据源的防火墙上使用自托管 IR 的公共静态 IP 地址，等等。

## <a name="prepare-self-hosted-ir"></a>准备自承载 IR

若要使用此功能，首先需要创建一个 ADF 并在其下预配 Azure-SSIS IR （如果尚未这样做），请按照[如何预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)一文进行操作。

然后，需要按照[如何创建自承载 ir](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)文章，在设置了 Azure-SSIS IR 的同一 ADF 中预配自承载 ir。

最后，你将需要在本地计算机上下载并安装最新版本的自承载 IR 以及其他驱动程序和运行时，如下所示：
- 从[此处](https://www.microsoft.com/download/details.aspx?id=39717)下载并安装最新版本的自承载 IR。
- 如果你使用包中的 OLEDB 连接器，请在安装了自承载 IR 的同一台计算机上下载并安装相关 OLEDB 驱动程序（如果尚未这样做）。  如果使用早期版本的 OLEDB driver for SQL Server （SQLNCLI.MSI），则可以从[此处](https://www.microsoft.com/download/details.aspx?id=50402)下载64位版本。  如果使用最新版本的 OLEDB driver for SQL Server （MSOLEDBSQL），则可以从[此处](https://www.microsoft.com/download/details.aspx?id=56730)下载64位版本。  如果对其他数据库系统（如 PostgreSQL、MySQL、Oracle 等）使用 OLEDB 驱动程序，则可从各自的网站下载64位版本。
- 在安装了自C++承载 IR 的同一计算机上下载并安装 Visual （VC）运行时（如果尚未这样做）。  你可以从[此处](https://www.microsoft.com/download/details.aspx?id=40784)下载64位版本。

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>为暂存准备 Azure Blob 存储链接服务

按照[如何创建 ADF 链接服务](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)一文在预配了你的 Azure-SSIS IR 的同一 ADF 中创建 Azure Blob 存储链接服务（如果尚未这样做）。  请确保以下各项：
- 已为**数据存储**选择**Azure Blob 存储**
- 已选择**AutoResolveIntegrationRuntime** **通过集成运行时进行连接**
- 为**身份验证方法**选择了**帐户密钥**/**SAS URI**/**服务主体**

>[!TIP]
>选择 " **服务主体** " 时，至少授予 " **存储 Blob 数据参与者" 角色**。 有关详细信息，请参阅 [Azure Blob 存储连接器](connector-azure-blob-storage.md#linked-service-properties)。

![为暂存准备 Azure Blob 存储链接服务](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>使用自承载 IR 作为代理来配置 Azure-SSIS IR

准备好用于过渡的自承载 IR 和 Azure Blob 存储链接服务之后，现在可以使用自承载 IR 将新的/现有 Azure-SSIS IR 配置为 ADF 门户/应用上的代理。  如果现有 Azure-SSIS IR 正在运行，请在执行此操作之前先将其停止，然后重新启动。

1. 在集成运行时设置面板上，通过选择 "**下一步**" 按钮前进到 "**常规设置**" 和 " **SQL 设置**" 部分。 

1. 在 "**高级设置**" 部分中：

   1. 选中 "**将自承载 Integration Runtime 设置为 Azure-SSIS Integration Runtime 的代理**" 复选框。 

   1. 对于**自承载 Integration Runtime**，选择现有的自承载 IR 作为 Azure-SSIS IR 的代理。

   1. 对于 "**暂存存储链接服务**"，请选择现有的 Azure Blob 存储链接服务，或创建一个新的临时存储。

   1. 对于**暂存路径**，请在所选的 Azure blob 存储帐户中指定 blob 容器，或将其留空以使用默认值进行暂存。

   1. 选择“继续”。

   ![使用自承载 IR 的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

你还可以使用 PowerShell 将新的/现有 Azure-SSIS IR 与自承载 IR 一起配置为代理。

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

将最新的 SSDT 与适用于 Visual Studio 的 SSIS 项目扩展结合使用，可以从[此处](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下载，或将其作为可从[此处](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)下载的独立安装程序，可找到已添加到 OLEDB/平面文件连接管理器中的新**ConnectByProxy**属性。  

当使用 OLEDB/平面文件源设计包含数据流任务的新包来访问本地数据库/文件时，可以启用此属性，方法是在相关连接管理器的 "属性" 面板上将其设置为**True** 。

![启用 ConnectByProxy 属性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

你还可以在运行现有包时启用此属性，而无需逐个手动更改它们。  有两个选项：
- 使用最新的 SSDT 打开、重新生成和重新部署包含这些包的项目，以在 Azure-SSIS IR 上运行：然后，可以通过将属性设置为 True，将其设置为**True** ，以便在运行 SSMS 的包时出现在 "执行包" 弹出窗口的 "**连接管理**器" 选项卡上。

  ![Enable ConnectByProxy property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  还可以通过将属性设置为 True，在 ADF 管道中运行包时，将 "[执行 SSIS 包" 活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)的 "**连接管理**器" 选项卡上显示的相关连接管理器设置为**True** 。
  
  ![Enable ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- 重新部署包含这些包的项目以在 SSIS IR 上运行：然后，可以通过提供属性路径来启用属性，`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，并将其设置为**True** ，以便在运行 SSMS 的包时在 "执行包" 弹出窗口的 "**高级**" 选项卡上进行属性重写。

  ![Enable ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  还可以通过在 ADF 管道中运行包时，提供属性路径、`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`，并将其设置为**True**作为 "[执行 SSIS 包" 活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)的 "**属性重**写" 选项卡上的属性重写，来启用属性。
  
  ![Enable ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>调试第一个和第二个临时任务

在自承载 IR 上，可以在 `C:\ProgramData\SSISTelemetry` "文件夹中找到运行时日志，并在 `C:\ProgramData\SSISTelemetry\ExecutionLog`" 文件夹中找到第一个暂存任务的执行日志。  可以在 SSISDB 或指定的日志记录路径中找到第二个过渡任务的执行日志，具体取决于你是将包分别存储在 SSISDB 中还是文件系统/文件共享/Azure 文件中。  第一个暂存任务的唯一 Id 还可以在第二个过渡任务的执行日志中找到，例如 

![第一个暂存任务的唯一 ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>在临时任务中使用 Windows 身份验证

如果在自承载 IR 上过渡任务需要 Windows 身份验证，则需要[将 SSIS 包配置为使用相同的 windows 身份验证](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 将使用自承载的 IR 服务帐户（默认`NT SERVICE\DIAHostService`）调用你的暂存任务，你的数据存储将使用 Windows 身份验证帐户进行访问。 这两个帐户都需要为其分配特定安全策略。 因此，在自承载 IR 计算机上，打开 `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` 并完成以下步骤。
- 分配 "**调整进程的内存配额**"，并将**进程级别令牌策略替换**为自承载的 IR 服务帐户。 这应该在安装具有默认服务帐户的自承载 IR 时自动完成。 如果使用不同的服务帐户，则为其分配相同的策略。
- 将 "**作为服务登录**" 策略分配给 Windows 身份验证帐户。

## <a name="billing-for-the-first-and-second-staging-tasks"></a>第一和第二个过渡任务的计费

在自承载 IR 上运行的第一个暂存任务将按与在自承载 IR 上运行的任何数据移动活动的相同方式进行计费，如[ADF 数据管道定价](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)一文中所述。

Azure-SSIS IR 上运行的第二个过渡任务不会单独计费，但正在运行的 Azure-SSIS IR 将按[Azure-SSIS IR 定价](https://azure.microsoft.com/pricing/details/data-factory/ssis/)文章中指定的方式计费。

## <a name="current-limitations"></a>当前限制

- 目前仅支持具有 ODBC/OLEDB/平面文件连接管理器和 ODBC/OLEDB/平面文件源或 OLEDB 目标的数据流任务。 
- 目前仅支持配置有**帐户密钥**/**SAS URI**/**服务主体**身份验证的 Azure Blob 存储链接服务。

## <a name="next-steps"></a>后续步骤

将你的自承载 IR 配置为你的 Azure-SSIS IR 的代理后，你可以部署和运行你的包，以便在 ADF 管道中作为 "执行 ssis 包" 活动访问本地数据，请参阅[在 adf 管道中将 ssis 包作为 "执行 Ssis 包" 活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
