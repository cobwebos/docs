---
title: 从 SSDT 执行 Azure 数据工厂中的 SSIS 包 |Microsoft Docs
description: 了解如何从 SSDT 在 Azure 中执行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9ddf427d7d749dff1af45b3f6f83d20a89e1aae0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678404"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>从 SSDT 在 Azure 中执行 SSIS 包
本文介绍 SQL Server Data Tools (SSDT) 上启用了 Azure 的 SQL Server Integration Services (SSIS) 项目的功能, 该功能允许你在 azure 数据工厂 (ADF) 中的 Azure-SSIS Integration Runtime (IR) 上运行包。  你可以使用此功能来测试现有 SSIS 包, 然后将其 & 提升或迁移到 Azure, 或开发新的 SSIS 包以在 Azure 中运行。

利用此功能, 可以创建新的 Azure SSIS IR, 或将现有的 Azure SSIS 附加到 SSIS 项目, 然后在其上执行包。  我们支持在项目部署模型中将包部署到 SSIS 目录 (SSISDB) 中, 以及要部署到包部署模型中的文件系统/文件共享/Azure 文件中的包。 

## <a name="prerequisites"></a>系统必备
若要使用此功能, 请从[此处](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下载并安装适用于 Visual Studio 的最新 SSDT, 并将其作为独立的安装[程序。](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-启用 SSIS 项目
在 SSDT 上, 可以使用**Integration Services 项目 (Azure 启用)** 模板创建启用了 azure 的新 SSIS 项目。

![启用了 Azure 的新 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

或者, 你可以通过右键单击 SSDT 的 "解决方案资源管理器" 面板中的项目节点, 然后选择 "**已启用 azure** " 菜单项, 来启用现有 SSIS 项目。

![Azure-启用现有 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure-启用现有 SSIS 项目时, 需要将其目标服务器版本设置为 Azure-SSIS IR 支持的最新版本, 该版本当前**SQL Server 2017**, 因此, 如果尚未执行此操作, 则会弹出一个对话框窗口来执行此操作。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>将支持 Azure 的项目连接到 Azure 数据工厂中的 SSIS
通过将启用了 Azure 的项目连接到 ADF 中的 SSIS, 你可以将包上传到 Azure 文件中, 并在 Azure 上运行它们。  为此, 请执行以下步骤:

1. 右键单击 "SSDT" 面板解决方案资源管理器中的 "项目" 或 "**链接的 Azure 资源**" 节点, 弹出一个菜单, 然后选择 "**连接到 Azure 数据工厂中的 ssis** " 菜单项, 以**在 ADF 连接向导中启动 ssis**。

   ![在 ADF 中连接到 SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. 在 " **ADF 简介**" 页的 "简介" 页上, 查看简介, 并单击 "**下一步**" 按钮继续。

   ![ADF 中的 SSIS 简介](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. 在 "**选择 adf 中的 SSIS ir** " 页上, 选择现有的 Adf 和 AZURE SSIS IR 来运行包, 或者创建新的文件 (如果没有)。
   - 若要选择现有的 Azure SSIS IR, 请首先选择相关的 Azure 订阅和 ADF。
   - 如果你选择的是没有任何 Azure SSIS IR 的现有 ADF, 请单击 "**创建 SSIS IR** " 按钮, 在 adf 门户/应用上创建一个新的。
   - 如果选择不具有任何 ADF 的现有 Azure 订阅, 请单击 "**创建 SSIS IR** " 按钮启动**Integration Runtime 创建向导**, 你可以在其中输入用于自动创建新 Azure 的位置和前缀资源组、数据工厂和 SSIS IR, 命名为以下模式:**YourPrefix/DF/YourCreationTime**。
   
   ![选择 ADF 中的 SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. 在 "**选择 Azure 存储**" 页上, 选择现有的 azure 存储帐户, 将包上传到 azure 文件中, 或创建新的 azure 存储帐户 (如果没有)。
   - 若要选择现有的 Azure 存储帐户, 请先选择相关的 Azure 订阅。
   - 如果你选择的 azure 订阅与没有任何 Azure 存储帐户的 Azure SSIS IR 相同, 请单击 "**创建 Azure 存储**" 按钮, 以便在 AZURE ssis ir 所在的同一位置自动创建新的订阅, 命名方式为结合使用 Azure SSIS IR 名称及其创建日期的前缀。
   - 如果选择不具有任何 Azure 存储帐户的其他 Azure 订阅, 请单击 "**创建 Azure 存储**" 按钮, 在 Azure 门户上创建一个新订阅。
   
   ![选择“Azure 存储”](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. 单击 "**连接**" 按钮以完成连接。  解决方案资源管理器在 SSDT 的 "**链接的 Azure 资源**" 节点下, 将显示所选的 AZURE SSIS IR 和 azure 存储帐户。  我们还将刷新 Azure SSIS IR 的状态, 同时可以通过右键单击其节点弹出菜单, 然后选择转到 ADF 门户/应用的**Start\Stop\Manage**菜单项来进行管理。

## <a name="execute-ssis-packages-in-azure"></a>在 Azure 中执行 SSIS 包
### <a name="starting-package-executions"></a>启动包执行
将项目连接到 ADF 中的 SSIS 后, 可以在 Azure-SSIS IR 上执行包。  可以使用两个选项来启动包执行:
-  单击 SSDT 工具栏中的 "**启动**" 按钮, 下拉菜单, 然后选择 "**在 Azure 中执行**" 菜单项 

   ![在 Azure 中执行](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  右键单击 SSDT 的 "解决方案资源管理器" 面板中的 "包" 节点, 弹出一个菜单, 然后选择 "**在 Azure 中执行包**" 菜单项。

   ![在 Azure 中执行包](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> 若要在 Azure 中执行包, 需要具有运行的 Azure SSIS IR, 因此, 如果 Azure SSIS IR 已停止, 则会弹出一个对话框窗口以启动它。  排除任何自定义设置时间, 此过程应在5分钟内完成, 但对于 Azure-SSIS IR, 加入虚拟网络大约需要 20-30 分钟。  在 Azure 中执行包后, 可以通过右键单击 SSDT 的 "解决方案资源管理器" 面板中的节点来弹出菜单, 然后选择转到 ADF 门户的**Start\Stop\Manage**菜单项来停止 AZURE SSIS IR, 以管理其运行成本。用于执行此操作的应用程序。

### <a name="checking-package-execution-logs"></a>检查包执行日志
在开始执行包时, 会在 SSDT 的 "进度" 窗口中设置格式并显示其日志。  对于长时间运行的包, 我们将按分钟定期更新其日志。  可以通过单击 SSDT 工具栏中将立即取消包的 "**停止**" 按钮来停止包执行。  你还可以临时查找其通用命名约定 (UNC) 路径中的日志原始数据: `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, 但我们会在一天后将其清除。

### <a name="switching-package-protection-level"></a>切换包保护级别
在 Azure 中执行 SSIS 包不支持**EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey**保护级别。  因此, 如果对包进行了配置, 则我们会将它们分别临时切换到**EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, 并在我们上传打包到 Azure 文件中, 以便在 Azure SSIS IR 上执行。

> [!NOTE]
> 如果包中包含的执行包任务引用配置了**EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey**保护级别的其他包, 则需要手动将这些其他包重新配置为执行包之前, 请分别使用**EncryptSensitiveWithPassword**/**EncryptAllWithPassword**。

如果已使用**EncryptSensitiveWithPassword**/**EncryptAllWithPassword**保护级别配置包, 我们会将其保持不变, 但当我们上传你的打包到 Azure 文件中, 以便在 Azure SSIS IR 上执行。

### <a name="using-package-configuration-file"></a>使用包配置文件
如果使用包部署模型中的包配置文件在运行时更改变量值, 我们会自动将包含包的这些文件上传到 Azure 文件中, 以便在 Azure SSIS IR 上执行。

### <a name="using-execute-package-task"></a>使用执行包任务
如果包中包含的执行包任务引用存储在本地文件系统上的其他包, 则需要执行以下附加设置:

1. 将其他包上传到与你的项目连接的同一 Azure 存储帐户下的 Azure 文件中, 并获取新的 UNC 路径, 例如`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 将执行包任务的文件连接管理器中其他包的文件路径替换为新的 UNC 路径
   - 如果运行 SSDT 的计算机无法访问新的 UNC 路径, 则可以在文件连接管理器的属性面板上更改文件路径
   - 或者, 你可以在运行时对文件路径使用变量来分配适当的值

如果包中包含引用同一项目中其他包的执行包任务, 则无需进行其他设置。

## <a name="next-steps"></a>后续步骤
在 Azure 中从 SSDT 运行包后, 可以在 ADF 管道中将其部署和运行为 "执行 SSIS 包" 活动, 请参阅[在 adf 管道中将 ssis 包作为 "执行 Ssis 包" 活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
