---
title: 将自托管集成运行时配置为 SSIS 的代理
description: 了解如何将自托管集成运行时配置为 Azure-SSIS 集成运行时的代理。
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
ms.date: 03/27/2020
ms.openlocfilehash: 9a1923057bc318869f491791520aacb4d0d17591
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346620"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>将自承载的 IR 配置为 Azure 数据工厂中的 Azure-SSIS IR 的代理

本文介绍如何在 Azure 数据工厂中的 Azure-SSIS 集成运行时 （Azure-SSIS IR） 上运行 SQL 服务器集成服务 （SSIS） 包，并配置为代理的自托管集成运行时（自托管 IR）。 

借助此功能，您可以在本地访问数据，而无需[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 当公司网络的配置过于复杂或策略过于严格，无法将 Azure-SSIS IR 注入其中时，此功能非常有用。

此功能将具有本地数据源的任何 SSIS 数据流任务拆分为两个暂存任务： 
* 第一个任务在自托管 IR 上运行，首先将数据从本地数据源移动到 Azure Blob 存储中的暂存区域。
* 第二个任务在 Azure-SSIS IR 上运行，然后将数据从暂存区域移动到预期数据目标。

例如，此功能的其他优点和功能允许您在 Azure-SSIS IR 尚未支持的区域设置自托管 IR，并允许在数据源的防火墙上使用自托管 IR 的公共静态 IP 地址。

## <a name="prepare-the-self-hosted-ir"></a>准备自托管的 IR

要使用此功能，请首先创建数据工厂并在其中设置 Azure-SSIS IR。 如果尚未这样做，请按照[设置 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)中的说明操作。

然后，在 Azure-SSIS IR 所在的同一数据工厂中设置自托管 IR。 为此，请参阅[创建自托管 IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。

最后，在本地计算机或 Azure 虚拟机 （VM） 上下载并安装最新版本的自托管 IR 以及其他驱动程序和运行时，如下所示：
- 下载并安装[最新版本的自托管 IR](https://www.microsoft.com/download/details.aspx?id=39717)。
- 如果在包中使用对象链接和嵌入数据库 （OLEDB） 连接器，请在安装自托管 IR 的同一台计算机上下载并安装相关的 OLEDB 驱动程序（如果尚未安装）。  

  如果将早期版本的 OLEDB 驱动程序用于 SQL Server（SQL Server 本机客户端 [SQLNCLI]），[请下载 64 位版本](https://www.microsoft.com/download/details.aspx?id=50402)。  

  如果您使用最新版本的 OLEDB 驱动程序进行 SQL 服务器 （MSOLEDBSQL），[请下载 64 位版本](https://www.microsoft.com/download/details.aspx?id=56730)。  
  
  如果将 OLEDB 驱动程序用于其他数据库系统（如 PostgreSQL、MySQL、Oracle 等），则可以从其网站下载 64 位版本。
- 如果尚未执行此操作，[请下载并安装 64 位版本的 Visual C++ （VC） 运行时](https://www.microsoft.com/download/details.aspx?id=40784)，该主机安装自托管 IR。

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>准备用于暂存的 Azure Blob 存储链接服务

如果尚未这样做，请在同一数据工厂中创建 Azure Blob 存储链接服务，并设置 Azure-SSIS IR。 为此，请参阅创建[Azure 数据工厂链接的服务](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)。 请务必执行以下操作：
- 对于**数据存储**，选择**Azure Blob 存储**。  
- 对于**通过集成运行时进行连接**，请选择 **"自动解析集成运行时**"（不是 Azure-SSIS IR 或自托管 IR），因为我们使用默认的 Azure IR 来获取 Azure Blob 存储的访问凭据  
- 对于**身份验证方法**，选择**帐户密钥** **、SAS URI****或服务主体**。  

    >[!TIP]
    >如果选择**服务主体**方法，请至少授予服务主体存储 Blob *数据参与者* 角色。 有关详细信息，请参阅 Azure [Blob 存储连接器](connector-azure-blob-storage.md#linked-service-properties)。

![准备用于暂存的 Azure Blob 存储链接服务](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>使用自托管 IR 配置 Azure-SSIS IR 作为代理

准备好自托管的 IR 和 Azure Blob 存储链接服务进行暂存后，现在可以将新的或现有的 Azure-SSIS IR 与自托管 IR 配置为数据工厂门户或应用中的代理。 但是，在执行此操作之前，如果现有的 Azure-SSIS IR 已在运行，请停止它，然后重新启动它。

1. 在 **"集成运行时设置"** 窗格中，通过选择 **"下一步**"跳过 **"常规设置**"和 **"SQL 设置"** 部分。 

1. 在 **"高级设置"** 部分中，执行以下操作：

   1. 选择"**设置自托管集成运行时作为 Azure-SSIS 集成运行时的代理**"复选框。 

   1. 在**自托管集成运行时**下拉列表中，选择现有的自托管 IR 作为 Azure-SSIS IR 的代理。

   1. 在**暂存存储链接服务**下拉列表中，选择现有的 Azure Blob 存储链接服务或创建新的暂存服务。

   1. 在 **"暂存"路径**框中，在选定的 Azure Blob 存储帐户中指定 Blob 容器，或将其留空以使用默认容器进行暂存。

   1. 选择“继续”****。

   ![具有自承载 IR 的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

您还可以使用 PowerShell 将新的或现有的 Azure-SSIS IR 与自托管 IR 配置为代理。

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>启用 SSIS 包通过代理连接

通过将最新的 SSDT 与 Visual Studio 的 SSIS 项目扩展名或独立安装程序使用，`ConnectByProxy`可以找到已在 OLEDB 或平面文件连接管理器中添加的新属性。
* [下载 SSDT 与 SSIS 项目扩展为可视化工作室](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [下载独立安装程序](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

当您使用 OLEDB 或 Flat File 源设计包含数据流任务的新包（允许在本地访问数据库或文件）时，可以通过在相关连接管理器的 **"属性"** 窗格中将其设置为*True*来启用此属性。

![启用 ConnectByProxy 属性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

在运行现有包时，还可以启用此属性，而无需逐个手动更改它们。  存在两个选项：
- **选项 A**： 打开、重新生成和重新部署包含具有最新 SSDT 的包的项目，以在 Azure-SSIS IR 上运行。 然后，您可以通过将属性设置为 *"为相关连接管理器"为 True"* 来启用该属性。 当他们从 SSMS 运行包时，这些连接管理器将显示在 **"执行包"** 弹出窗口的连接**管理器**选项卡上。

  ![启用 ConnectByProxy 属性2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  您还可以通过将在数据工厂管道中运行包时显示在["执行 SSIS 包活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)**的连接管理器**"选项卡上的相关连接管理器设置为*True*来启用该属性。
  
  ![启用 ConnectByProxy 属性3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **选项 B：** 重新部署包含这些包的项目，以在 SSIS IR 上运行。 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`然后，您可以在从 SSMS 运行包时，通过提供属性路径，并将其设置为*True，* 将其设置为 **"执行包**"弹出窗口的 **"高级**"选项卡上的属性覆盖。

  ![启用 ConnectByProxy 属性4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  还可以通过提供`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`属性路径来启用该属性，并在数据工厂管道中运行包时，将其设置为*True*作为属性覆盖"执行[SSIS 包活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)**的属性覆盖**"选项卡。
  
  ![启用 ConnectByProxy 属性5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>调试第一个和第二个暂存任务

在自托管 IR 上，您可以在*C：_程序数据_SSIS遥测*文件夹中找到运行时日志，并在*C：*程序数据_SSIS遥测_执行日志*文件夹中找到第一个暂存任务的执行日志。  您可以在 SSISDB 或指定的日志记录路径中找到第二个暂存任务的执行日志，具体取决于将包存储在 SSISDB 或文件系统、文件共享还是 Azure 文件中。 您还可以在第二个暂存任务的执行日志中找到第一个暂存任务的唯一 ID。 

![第一个暂存任务的唯一 ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>在暂存任务中使用 Windows 身份验证

如果自托管 IR 上的暂存任务需要 Windows 身份验证，[请将 SSIS 包配置为使用相同的 Windows 身份验证](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 

您的暂存任务将使用自托管的 IR 服务帐户 *（NT SERVICE_DIAHostService，* 默认情况下）调用，并且您的数据存储将使用 Windows 身份验证帐户访问。 这两个帐户都需要为其分配某些安全策略。 在自托管 IR 计算机上，转到**本地安全策略** > **本地策略本地策略** > **用户权限分配**，然后执行以下操作：

1. 为*进程分配"调整内存配额*"，并将*进程级令牌策略替换为*自托管的 IR 服务帐户。 当您使用默认服务帐户安装自托管的 IR 时，应会自动执行此操作。 如果没有，请手动分配这些策略。 如果您使用不同的服务帐户，请为其分配相同的策略。

1. 将*登录作为服务*策略分配给 Windows 身份验证帐户。

## <a name="billing-for-the-first-and-second-staging-tasks"></a>第一个和第二个暂存任务的计费

在自托管 IR 上运行的第一个暂存任务单独计费，就像对在自托管 IR 上运行的任何数据移动活动进行计费一样。 这在[Azure 数据工厂数据管道定价](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)文章中指定。

在 Azure-SSIS IR 上运行的第二个暂存任务不单独计费，但正在运行的 Azure-SSIS IR 将在[Azure-SSIS IR 定价](https://azure.microsoft.com/pricing/details/data-factory/ssis/)文章中指定计费。

## <a name="enabling-tls-12"></a>启用 TLS 1.2

如果您需要使用强加密/更安全的网络协议 （TLS 1.2） 并在自托管的 IR 上禁用较旧的 SSL/TLS 版本，则可以下载并运行*主.cmd*脚本，该脚本可在我们的公共预览容器的*CustomSetupScript/UserS/TLS 1.2*文件夹中找到。  使用[Azure 存储资源管理器](https://storageexplorer.com/)，可以通过输入以下 SAS URI 连接到我们的公共预览容器：

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>当前限制

- 目前仅支持具有开放数据库连接 （ODBC）/OLEDB/平面文件源的数据流任务。 
- 目前仅支持配置帐户*密钥*、*共享访问签名 （SAS） URI*或*服务主体*身份验证的 Azure Blob 存储链接服务。
- 目前不支持在 OLEDB 源中*进行参数映射*。 作为解决方法，请使用 SQL*命令"从变量*"作为*AccessMode，* 并使用*表达式*在 SQL 命令中插入变量/参数。 例如，请参阅可在公共预览容器的*Self托管IRProxy/限制*文件夹中找到的*参数映射Sample.dtsx*包。 使用 Azure 存储资源管理器，您可以通过输入上述 SAS URI 连接到我们的公共预览容器。

## <a name="next-steps"></a>后续步骤

将自承载的 IR 配置为 Azure-SSIS IR 的代理后，可以部署和运行包以在本地访问数据，如在数据工厂管道中执行 SSIS 包活动。 要了解如何，请参阅[在数据工厂管道中将 SSIS 包作为执行 SSIS 包活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
