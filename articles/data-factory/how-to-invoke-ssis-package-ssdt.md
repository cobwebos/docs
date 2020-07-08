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
ms.openlocfilehash: 1d8261d05f59c7f40ba6b1e2d59d2b15ad56de95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424504"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>从 SSDT 在 Azure 中执行 SSIS 包

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍 SQL Server Data Tools （SSDT）上启用了 Azure 的 SQL Server Integration Services （SSIS）项目的功能，该功能允许你在 Azure 数据工厂（ADF）中的 Azure-SSIS Integration Runtime （IR）上运行包。  你可以使用此功能来测试现有 SSIS 包，然后将其 & 提升或迁移到 Azure，或开发新的 SSIS 包以在 Azure 中运行。

利用此功能，你可以创建新的 Azure-SSIS IR，或将现有的一个连接到 SSIS 项目，然后在其上执行包。  我们支持在项目部署模型中将包部署到 SSIS 目录（SSISDB）中，以及要部署到包部署模型中的文件系统/文件共享/Azure 文件中的包。 

## <a name="prerequisites"></a>先决条件
若要使用此功能，请从[此处](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下载并安装适用于 Visual Studio 的最新 SSDT，并将其作为独立的安装[程序。](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-启用 SSIS 项目
### <a name="create-new-azure-enabled-ssis-projects"></a>创建新的启用 Azure 的 SSIS 项目
在 SSDT 上，可以使用**Integration Services 项目（Azure 启用）** 模板创建启用了 azure 的新 SSIS 项目。

   ![启用了 Azure 的新 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

创建支持 Azure 的项目之后，系统会提示连接到 Azure 数据工厂中的 SSIS。

   ![连接 Azure-SSIS IR 提示符](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

如果要立即连接到 Azure-SSIS IR，请参阅[连接到 Azure-SSIS IR](#irconnect)了解详细信息。 稍后，你还可以通过右键单击 SSDT 的 "解决方案资源管理器" 面板中的项目节点，弹出一个菜单，然后选择 "Azure 数据工厂" 子菜单中**ssis**下的 "**在 azure 数据工厂中连接到 ssis** " 菜单项。

### <a name="azure-enable-existing-ssis-projects"></a>Azure-启用现有 SSIS 项目
对于现有 SSIS 项目，可以通过执行以下步骤，对其进行 Azure 启用：

1. 右键单击 SSDT 的 "解决方案资源管理器面板" 中的项目节点，弹出一个菜单，然后在 " **Azure 数据工厂**" 子菜单中的 "SSIS" 下选择 "**启用了 azure 的项目**" 菜单项，以启动启用了 azure 的**项目向导**。

   ![Azure-启用现有 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. 在 "**选择 Visual Studio 配置**" 页上，选择 "visual studio 配置" 以在 Azure 中应用包执行设置。 一种很好的做法是为 cloud 和 Azure 创建新的 Visual Studio 配置，使项目针对云配置。 通过多个配置，你可以根据不同的环境（在本地或在 Azure 中）为参数分配不同的值。 有关更多详细信息，请参阅[此示例](#example)。

   ![选择 Visual Studio 配置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure-启用现有 SSIS 项目后，需要将其目标服务器版本设置为 Azure-SSIS IR 支持的最新版本，当前**SQL Server 2017**。 如果你尚未执行此操作，则需要检查你的包是否包含 SQL Server 2017 不支持的其他组件，并单击 "下一步" 按钮以在不考虑的情况下继续。

   ![切换目标服务器版本](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. 请参阅[连接到 Azure-SSIS IR](#irconnect)以完成到 Azure-SSIS IR 的连接。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>将支持 Azure 的项目连接到 Azure 数据工厂中的 SSIS

通过将启用了 Azure 的项目连接到 ADF 中的 SSIS，你可以将包上传到 Azure 文件中，并在 Azure-SSIS IR 上运行它们。 为此，请执行以下步骤：

1. 在 " **ADF 简介**" 页的 "简介" 页上，查看简介，并单击 "**下一步**" 按钮继续。

   ![ADF 中的 SSIS 简介](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. 在 "**选择 adf 中的 SSIS IR** " 页上，选择现有 ADF 并 Azure-SSIS IR 运行包，或者创建新的 adf （如果没有）。
   - 若要选择现有 Azure-SSIS IR，请先选择相关的 Azure 订阅和 ADF。
   - 如果选择的是没有任何 Azure-SSIS IR 的现有 ADF，请单击 "**创建 SSIS IR** " 按钮，在 adf 门户/应用上创建一个新的。
   - 如果选择不具有任何 ADF 的现有 Azure 订阅，请单击 "**创建 SSIS IR** " 按钮启动**Integration Runtime 创建向导**，可以在其中输入用于代表你自动创建新的 Azure 资源组、数据工厂和 SSIS IR 的位置和前缀，按以下模式命名： **YourPrefix/YourCreationTime**。

   ![选择 ADF 中的 SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. 在 "**选择 Azure 存储**" 页上，选择现有的 azure 存储帐户，将包上传到 azure 文件中，或创建新的 azure 存储帐户（如果没有）。
   - 若要选择现有的 Azure 存储帐户，请先选择相关的 Azure 订阅。
   - 如果你选择的 Azure 订阅与你的 Azure-SSIS IR 没有任何 Azure 存储帐户，请单击 "**创建 Azure 存储**" 按钮，以在与你的 Azure-SSIS IR 相同的位置自动创建一个新的订阅，通过将 Azure-SSIS IR 名称和创建日期的前缀相结合来命名。
   - 如果选择不具有任何 Azure 存储帐户的其他 Azure 订阅，请单击 "**创建 Azure 存储**" 按钮，在 Azure 门户上创建一个新订阅。

   ![选择“Azure 存储”](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. 单击 "**连接**" 按钮以完成连接。  我们会在 SSDT 的 "**链接的 Azure 资源**" 解决方案资源管理器节点下显示所选 Azure-SSIS IR 和 azure 存储帐户。  我们还将刷新 Azure-SSIS IR 的状态，同时可以通过右键单击其节点弹出菜单，然后选择转到 ADF 门户/应用的**Start\Stop\Manage**菜单项来进行管理。

## <a name="execute-ssis-packages-in-azure"></a>在 Azure 中执行 SSIS 包
### <a name="azure-enabled-setting"></a>启用 Azure 的设置
在 Azure 中执行包之前，可以选择配置执行设置。 如果要为 SSIS 包启用 Windows 身份验证，请执行以下步骤：

1. 右键单击 SSDT 的 "解决方案资源管理器面板" 中的项目节点，弹出一个菜单，然后选择 " **Azure 数据工厂中的 SSIS** " 子菜单下的 "**启用了 azure 的设置**" 菜单项。

   ![启用 Azure 的设置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. 单击 "**启用 Windows 身份验证**" 下拉列表，然后选择 " **True**"。 然后单击 "编辑**Windows 身份验证凭据**的按钮" 选项以输入凭据。

   ![启用 Windows 身份验证](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. 在**Windows 身份验证凭据**编辑器中提供凭据。

   ![Windows 身份验证凭据](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>启动包执行
将项目连接到 ADF 中的 SSIS 后，可以在 Azure-SSIS IR 上执行包。  可以使用两个选项来启动包执行：
-  单击 SSDT 工具栏中的 "**启动**" 按钮，下拉菜单，然后选择 "**在 Azure 中执行**" 菜单项 

   ![在 Azure 中执行](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  右键单击 SSDT 的 "解决方案资源管理器" 面板中的 "包" 节点，弹出一个菜单，然后选择 "**在 Azure 中执行包**" 菜单项。

   ![在 Azure 中执行包](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> 若要在 Azure 中执行包，需要具有运行的 Azure-SSIS IR，因此，如果 Azure-SSIS IR 已停止，则会弹出一个对话框窗口以启动它。  此过程应在 5 分钟内完成（不包括任何自定义设置时间），但对于加入虚拟网络的 Azure-SSIS IR，此过程可能需要大约 20 - 30 分钟。  在 Azure 中执行包后，可以通过右键单击 SSDT 的 "解决方案资源管理器面板" 中的节点来停止 Azure-SSIS IR 来管理其运行成本，以弹出菜单，然后选择转到 ADF 门户/应用的**Start\Stop\Manage**菜单项来执行此操作。

### <a name="checking-package-execution-logs"></a>检查包执行日志
在开始执行包时，会在 SSDT 的 "进度" 窗口中设置格式并显示其日志。  对于长时间运行的包，我们将按分钟定期更新其日志。  可以通过单击 SSDT 工具栏中将立即取消包的 "**停止**" 按钮来停止包执行。  你还可以临时查找其通用命名约定（UNC）路径中的日志原始数据： `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` ，但我们会在一天后将其清除。

### <a name="switching-package-protection-level"></a>切换包保护级别
在 Azure 中执行 SSIS 包不支持**EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey**保护级别。  因此，如果对包进行了配置，则**EncryptSensitiveWithPassword** / 在将包上传到要在 Azure-SSIS IR 上执行的 Azure 文件时，会分别将它们分别切换到 EncryptSensitiveWithPassword**EncryptAllWithPassword**中。

> [!NOTE]
> 如果包中包含的执行包任务引用配置了**EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey**保护级别的其他包，则需要在执行包之前手动重新配置这些其他包以使用**EncryptSensitiveWithPassword** / **EncryptAllWithPassword**。

如果已使用**EncryptSensitiveWithPassword** / **EncryptAllWithPassword**保护级别配置包，我们会将其保持不变，但当我们将包上传到 Azure 文件以便在 Azure-SSIS IR 上执行时，仍将使用随机生成的密码。

### <a name="using-package-configuration-file"></a>使用包配置文件
如果使用包部署模型中的包配置文件在运行时更改变量值，我们会自动将包含包的这些文件上传到 Azure 文件中，以便在 Azure-SSIS IR 上执行。

### <a name="using-execute-package-task"></a>使用执行包任务
如果包中包含的执行包任务引用存储在本地文件系统上的其他包，则需要执行以下附加设置：

1. 将其他包上传到与你的项目连接的同一 Azure 存储帐户下的 Azure 文件中，并获取新的 UNC 路径，例如`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 将执行包任务的文件连接管理器中其他包的文件路径替换为新的 UNC 路径
   - 如果运行 SSDT 的计算机无法访问新的 UNC 路径，则可以在文件连接管理器的属性面板上更改文件路径
   - 或者，你可以在运行时对文件路径使用变量来分配适当的值

如果包中包含引用同一项目中其他包的执行包任务，则无需进行其他设置。

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>将包执行环境切换为启用了 Azure 的项目

若要使用启用了 Azure 的项目切换包执行环境，可以创建多个 Visual Studio 配置，以便为特定于环境的参数应用不同的值。 例如，有一个具有**文件系统任务**的简单 SSIS 包，该任务用于设置指定文件的属性，只需使用以下步骤将其迁移到云：

1. 定义 string 类型的参数**FilePath** ，它是目标文件的文件路径。

   ![定义包参数](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. 将**源连接**与此参数关联。 

   ![更新源连接](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. 在 Visual Studio 中为 cloud 创建新的 Visual Studio 配置 Configuration Manager。

4. 针对每个 Visual Studio 配置定义此参数的值。

   ![重写参数值](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. 针对云的 Visual Studio 配置启用了 Azure 此 SSIS 项目。

6. 在 Azure 中执行此包。 通过切换当前的 Visual Studio 配置，可以轻松地将环境切换回本地环境。

   ![切换 Visual Studio 配置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="next-steps"></a>后续步骤
在 Azure 中从 SSDT 运行包后，可以在 ADF 管道中将其部署和运行为 "执行 SSIS 包" 活动，请参阅[在 adf 管道中将 ssis 包作为 "执行 Ssis 包" 活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
