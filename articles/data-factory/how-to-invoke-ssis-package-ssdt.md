---
title: 从 SSDT 执行 SSIS 包
description: 了解如何从 SSDT 在 Azure 中执行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399428"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>从 SSDT 在 Azure 中执行 SSIS 包

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍 SQL Server 数据工具 （SSDT） 上启用 Azure 的 SQL 服务器集成服务 （SSIS） 项目的功能，它允许您在 Azure 数据工厂 （ADF） 中运行 Azure-SSIS 集成运行时 （IR） 上的包。  在提升&移位/迁移到 Azure 或开发要在 Azure 中运行的新 SSIS 包之前，可以使用此功能测试现有 SSIS 包。

使用此功能，您可以创建新的 Azure-SSIS IR 或将现有 IR 附加到 SSIS 项目，然后执行其包。  我们支持在项目部署模型中将正在运行的包部署到 SSIS 目录 （SSISDB） 中，支持在包部署模型中将运行的包部署到文件系统/文件共享/Azure 文件中。 

## <a name="prerequisites"></a>先决条件
要使用此功能，请[在此处](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下载并安装最新的 SSDT 与 Visual Studio 的 SSIS 项目扩展，或[在此处](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)作为独立安装程序。

## <a name="azure-enable-ssis-projects"></a>启用 Azure 的 SSIS 项目
在 SSDT 上，可以使用**集成服务项目（启用 Azure）** 模板创建新启用 Azure 的 SSIS 项目。

![启用 Azure 的新 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

或者，可以通过右键单击 SSDT 解决方案资源管理器面板中的项目节点来 Azure 启用现有 SSIS 项目，以弹出菜单，然后选择启用**Azure 的**菜单项。

![Azure 启用现有 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

启用 Azure 的现有 SSIS 项目需要将其目标服务器版本设置为 Azure-SSIS IR 支持的最新版本，当前为**SQL Server 2017，** 因此，如果尚未启用，将弹出一个对话框窗口来执行此操作。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>将启用 Azure 的项目连接到 Azure 数据工厂中的 SSIS
通过将启用 Azure 的项目连接到 ADF 中的 SSIS，可以将包上载到 Azure 文件中，并在 Azure-SSIS IR 上运行它们。  为此，请按照以下步骤操作：

1. 右键单击 SSDT 解决方案资源管理器面板中的项目或**链接 Azure 资源**节点以弹出菜单，并在 Azure**数据工厂菜单项中选择"连接到 SSIS"** 以**在 ADF 连接向导中启动 SSIS。**

   ![连接到 ADF 中的 SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. 在**ADF 简介中的 SSIS**页面上，查看简介并单击"**下一步**"按钮以继续。

   ![ADF 简介中的 SSIS](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. 在**ADF 页的"选择 SSIS IR"上**，选择现有的 ADF 和 Azure-SSIS IR 以运行包或创建新包（如果没有）。
   - 要选择现有的 Azure-SSIS IR，请先选择相关的 Azure 订阅和 ADF。
   - 如果选择没有任何 Azure-SSIS IR 的现有 ADF，请单击 **"创建 SSIS IR"** 按钮在 ADF 门户/应用中创建新的 ADF。
   - 如果选择没有任何 ADF 的现有 Azure 订阅，请单击 **"创建 SSIS IR"** 按钮以启动**集成运行时创建向导**，您可以在其中输入位置和前缀，以便我们代表您自动创建新的 Azure 资源组、数据工厂和 SSIS IR，以以下模式命名：**您的前缀-RG/DF/IR-您的创建时间**。
   
   ![在 ADF 中选择 SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. 在 **"选择 Azure 存储**"页上，选择现有 Azure 存储帐户以将包上载到 Azure 文件中，或者创建一个新包（如果没有）。
   - 要选择现有的 Azure 存储帐户，请先选择相关的 Azure 订阅。
   - 如果选择与 Azure-SSIS IR 相同的 Azure 订阅，而 Azure-SSIS IR 没有任何 Azure 存储帐户，请单击"**创建 Azure 存储**"按钮，以自动在 Azure-SSIS IR 相同的位置代表您创建新订阅，该订阅通过合并 Azure-SSIS IR 名称的前缀及其创建日期而命名。
   - 如果选择不包含任何 Azure 存储帐户的不同 Azure 订阅，请单击"创建**Azure 存储**"按钮在 Azure 门户上创建新订阅。
   
   ![选择“Azure 存储”](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. 单击"**连接**"按钮完成连接。  我们将在 SSDT 解决方案资源管理器面板中的 **"链接 Azure 资源**"节点下显示所选的 Azure-SSIS IR 和 Azure 存储帐户。  我们还将刷新 Azure-SSIS IR 的状态，同时可以通过右键单击其节点来弹出菜单，然后选择 **"开始\停止\管理"** 菜单项来执行此操作，从而对其进行管理。

## <a name="execute-ssis-packages-in-azure"></a>在 Azure 中执行 SSIS 包
### <a name="starting-package-executions"></a>启动包执行
在 ADF 中将项目连接到 SSIS 后，可以在 Azure-SSIS IR 上执行包。  有两个选项可以启动包执行：
-  单击 SSDT 工具栏中的 **"开始"** 按钮，下拉菜单并选择**Azure 菜单项中的"执行"** 

   ![在 Azure 中执行](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  右键单击 SSDT 解决方案资源管理器面板中的包节点以弹出菜单并选择 Azure 菜单**项中的"执行包**"。

   ![在 Azure 中执行包](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> 在 Azure 中执行包需要具有正在运行的 Azure-SSIS IR，因此，如果 Azure-SSIS IR 已停止，将弹出一个对话框窗口来启动它。  此过程应在 5 分钟内完成（不包括任何自定义设置时间），但对于加入虚拟网络的 Azure-SSIS IR，此过程可能需要大约 20 - 30 分钟。  在 Azure 中执行包后，可以通过右键单击 SSDT 解决方案资源管理器面板中的节点以弹出菜单，然后选择 **"开始\停止\管理"** 菜单项来阻止 Azure-SSIS IR 来管理其运行成本。

### <a name="checking-package-execution-logs"></a>检查包执行日志
当您开始执行包时，我们将在 SSDT 的"进度"窗口中格式化并显示其日志。  对于长时间运行的包，我们将按分钟定期更新其日志。  您可以通过单击 SSDT 工具栏中的 **"停止"** 按钮来阻止包执行，该按钮将立即取消它。  您还可以在其通用命名约定 （UNC） 路径中暂时找到日志原始数据： `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`，但我们将在一天后清理它。

### <a name="switching-package-protection-level"></a>切换封装保护级别
在 Azure 中执行 SSIS 包不支持**加密敏感用户密钥**/**加密AllUserKey**保护级别。  因此，如果包配置了这些包，我们将在将包上载到 Azure 文件以在 Azure-SSIS IR 上执行时，分别用随机生成的密码将它们切换到**加密敏感密码**/**加密所有密码**。

> [!NOTE]
> 如果包包含引用使用**加密敏感与UserKey**/**EncryptAllUserKey**保护级别配置的其他包的执行包任务，则需要在执行包之前手动重新配置这些其他包以分别使用**加密敏感密码**/**加密AllWithPassword。**

如果您的包已配置了**加密敏感密码**/**加密所有密码**保护级别，我们将保持它们不变，但在将包上载到 Azure 文件中以在 Azure-SSIS IR 上执行时，仍将使用随机生成的密码。

### <a name="using-package-configuration-file"></a>使用包配置文件
如果在包部署模型中使用包配置文件在运行时更改变量值，我们将会自动将包含包的文件上载到 Azure 文件中，以便在 Azure-SSIS IR 上执行。

### <a name="using-execute-package-task"></a>使用执行包任务
如果包包含引用存储在本地文件系统上的其他包的执行包任务，则需要执行以下其他设置：

1. 将其他包上载到连接到项目的同一 Azure 存储帐户下的 Azure 文件中，并获取其新的 UNC 路径，例如。`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 将执行包任务的文件连接管理器中这些其他包的文件路径替换为其新的 UNC 路径
   - 如果运行 SSDT 的计算机无法访问新的 UNC 路径，则可以更改文件连接管理器的"属性"面板上的文件路径
   - 或者，您可以使用变量进行文件路径，在运行时分配正确的值

如果包包含引用同一项目中其他包的执行包任务，则无需其他设置。

## <a name="next-steps"></a>后续步骤
从 SSDT 在 Azure 中运行包后，可以在 ADF 管道中部署并运行这些包作为执行 SSIS 包活动，请参阅[在 ADF 管道中运行 SSIS 包活动](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
