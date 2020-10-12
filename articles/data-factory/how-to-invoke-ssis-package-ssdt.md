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
ms.date: 09/06/2020
ms.openlocfilehash: fb5b5cb0ac4a9ace7b5de5e92308da58fd2b1fec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504938"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>从 SSDT 在 Azure 中执行 SSIS 包

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍了 Azure 支持的 SQL Server Integration Services (SSIS) 项目的功能 SQL Server Data Tools (SSDT) 。 它可让你评估 SSIS 包的云兼容性，并 Azure-SSIS Integration Runtime Azure 数据工厂中的 (IR)  (ADF) 运行这些兼容性。 你可以使用此功能来测试现有包，然后将其 & 提升或迁移到 Azure，或者开发要在 Azure 中运行的新包。

使用此功能，你可以将新创建的/现有 Azure-SSIS IR 附加到 SSIS 项目，然后在其上执行包。  我们支持正在运行的包部署到 SSIS 目录中 (SSISDB) 由 Azure SQL 数据库服务器或项目部署模型中的托管实例承载。 我们还支持将正在运行的包部署到文件系统/Azure 文件/SQL Server 数据库 (MSDB) 托管在包部署模型中的 Azure SQL 托管实例。 

## <a name="prerequisites"></a>必备条件

若要使用此功能，请从 [此处](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下载并安装适用于 Visual Studio (与) 的 SSIS 项目扩展的最新 SSDT。 此外，还可以从 [此处](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)下载并安装最新的 SSDT 作为独立安装程序。

## <a name="azure-enable-ssis-projects"></a>Azure-启用 SSIS 项目

### <a name="creating-new-azure-enabled-ssis-projects"></a>创建新的启用 Azure 的 SSIS 项目

在 SSDT 上，可以使用 **Integration Services 项目 (启用了 azure 的) ** 模板创建启用了 azure 的新 SSIS 项目。

   ![启用了 Azure 的新 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

创建支持 Azure 的项目之后，系统会提示连接到 Azure 数据工厂中的 SSIS。

   ![连接 Azure-SSIS IR 提示符](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

如果要立即连接到 Azure-SSIS IR，请参阅 [连接到 Azure-SSIS IR](#connectssisir) 了解更多详细信息。 稍后，还可以通过右键单击 SSDT 的 "解决方案资源管理器" 窗口中的项目节点，弹出菜单。 接下来，在**Azure 数据工厂**子菜单的 SSIS 中，选择 "**连接到 azure 数据工厂中的 ssis** " 项。

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure-启用现有 SSIS 项目

对于现有 SSIS 项目，可以通过执行以下步骤，对其进行 Azure 启用：

1. 右键单击 SSDT 的 "解决方案资源管理器" 窗口中的项目节点，弹出菜单。 接下来，在 " **Azure 数据工厂**" 子菜单中选择 "在 SSIS 中**启用了 azure 的项目**项"，以启动启用了 azure 的**项目向导**。

   ![Azure-启用现有 SSIS 项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. 在 " **选择 Visual Studio 配置** " 页上，选择现有的 VS 配置以在 Azure 中应用包执行设置。 如果尚未这样做，还可以创建一个新的 [VS 配置](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019)。 建议你在本地和云环境中为包执行至少使用两个不同的 VS 配置，因此你可以针对云配置启用你的项目。 这样，如果已将项目或包参数化，则可以在运行时基于 (本地计算机或 Azure) 中的不同执行环境，为项目或包参数分配不同的值。 例如，请参阅 [切换包执行环境](#switchenvironment)。

   ![选择 Visual Studio 配置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure-启用现有 SSIS 项目需要将其目标服务器版本设置为 Azure-SSIS IR 支持的最新版本。 Azure-SSIS IR 当前基于 **SQL Server 2017**。 请确保你的包不包含 SQL Server 2017 上不支持的其他组件。 还要确保还通过自定义设置在 Azure-SSIS IR 上安装了所有兼容的其他组件，请参阅 [自定义 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。 选择 " **下一步** " 按钮继续。

   ![切换目标服务器版本](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. 请参阅 [连接到 Azure-SSIS IR](#connectssisir) 以完成将项目连接到 Azure-SSIS IR。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> 将支持 Azure 的项目连接到 Azure 数据工厂中的 SSIS

通过将启用了 Azure 的项目连接到 ADF 中的 SSIS，你可以将包上传到 Azure 文件中，并在 Azure-SSIS IR 上运行它们。 为此，可以执行以下步骤：

1. 在 " **ADF 简介** " 页的 "简介" 页上，查看简介，然后选择 " **下一步** " 按钮继续。

   ![ADF 中的 SSIS 简介](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. 在 " **选择 adf 中的 SSIS IR** " 页上，选择现有 ADF 并 Azure-SSIS IR 运行包。 如果没有任何文件，也可以创建新的。
   - 若要选择现有 Azure-SSIS IR，请先选择相关的 Azure 订阅和 ADF。
   - 如果选择的是没有任何 Azure-SSIS IR 的现有 ADF，请选择 " **创建 SSIS IR** " 按钮，在 adf 门户中创建一个新的。 创建后，可以返回到此页以选择新 Azure-SSIS IR。
   - 如果选择不具有任何 ADF 的现有 Azure 订阅，请选择 " **创建 SSIS IR** " 按钮启动 **Integration Runtime 创建向导**。 在向导中，你可以输入指定的位置和前缀，以便我们以你的名义自动创建新的 Azure 资源组、数据工厂和 SSIS IR，并以以下模式命名： **YourPrefix/YourCreationTime**。 创建后，可以返回到此页以选择新 ADF 并 Azure-SSIS IR。

   ![选择 ADF 中的 SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. 在 " **选择 Azure 存储** " 页上，选择现有的 azure 存储帐户，将包上传到 azure 文件。 如果没有任何，还可以创建一个新的。
   - 若要选择现有的 Azure 存储帐户，请先选择相关的 Azure 订阅。
   - 如果选择的 Azure 订阅与没有任何 Azure 存储帐户的 Azure-SSIS IR 相同，请选择 " **创建 Azure 存储** " 按钮。 我们会在与你的 Azure-SSIS IR 相同的位置自动创建一个新的，并将你的 Azure-SSIS IR 名称及其创建日期的前缀组合在一起。 创建后，可以返回到此页以选择新的 Azure 存储帐户。
   - 如果选择不具有任何 Azure 存储帐户的其他 Azure 订阅，请选择 " **创建 Azure 存储** " 按钮，在 Azure 门户上创建一个新订阅。 创建后，可以返回到此页以选择新的 Azure 存储帐户。

   ![选择“Azure 存储”](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. 选择 " **连接** " 按钮，完成将项目连接到 Azure-SSIS IR。 我们会在 SSDT 的解决方案资源管理器窗口中的 "链接的 **Azure 资源** " 节点下显示所选 Azure-SSIS IR 和 Azure 存储帐户。 我们还会定期刷新并显示 Azure-SSIS IR 的状态。 可以通过右键单击其节点弹出菜单，然后选择 " **Start\Stop\Manage** " 项，将你转到 ADF 门户来管理你的 Azure-SSIS IR。

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>评估 SSIS project\packages 以在 Azure 中执行

### <a name="assessing-single-or-multiple-packages"></a>评估单个或多个包

在 Azure 中执行包之前，你可以对其进行评估，以呈现任何可能的云兼容性问题。 其中包括应注意的迁移阻止程序和其他信息。 
-  你可以选择在项目中同时按一次或所有包来评估单个包。

   ![评估包](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![评估项目](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  在 SSDT 的 " **评估报告** " 窗口中，可以找到所有潜在的云兼容性问题，每个问题都有其自己的详细说明和建议。 你还可以将评估报表导出为 CSV 文件，该文件可以与应缓解这些问题的任何人共享。 

   ![评估报告](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>抑制评估规则

确定某些潜在的云兼容性问题不适用于或已在包中正确缓解后，你可以抑制相关的评估规则。 这会减少后续评估报表的干扰。
-  在 SSDT 的 "**评估报告**" 窗口中选择 "**配置评估规则禁止**" 链接，以弹出 "**评估规则禁止显示设置**" 窗口，你可以在其中选择要抑制的评估规则。

   ![评估规则禁止设置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  或者，右键单击 SSDT 的 "解决方案资源管理器" 窗口中的项目节点，弹出菜单。 选择 " **Azure 数据工厂中的 SSIS** " 子菜单中的 "**启用了 Azure 的设置**" 项，弹出包含项目属性页的窗口。 在 "**启用了 Azure 的设置**" 部分中，选择 "**抑制评估规则 id** " 属性。 最后，选择其省略号 (**...** ") 按钮，弹出" **评估规则禁止显示设置** "窗口，您可以在其中选择要抑制的评估规则。

   ![启用 Azure 的设置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![通过 Azure 启用的设置评估规则禁止显示设置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>在 Azure 中执行 SSIS 包

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> 配置启用了 Azure 的设置

在 Azure 中执行包之前，可以为其配置启用了 Azure 的设置。 例如，你可以通过执行以下步骤，在你的 Azure-SSIS IR 上启用 Windows 身份验证以访问本地/云数据存储：

1. 右键单击 SSDT 的 "解决方案资源管理器" 窗口中的项目节点，弹出菜单。 接下来，选择 " **Azure 数据工厂中的 SSIS** " 子菜单中的 "**启用了 Azure 的设置**" 项，弹出包含项目属性页的窗口。

   ![启用 Azure 的设置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. 在 "**启用了 Azure 的设置**" 部分中选择 "**启用 Windows 身份验证**" 属性，然后在其下拉菜单中选择 " **True** "。 接下来，选择 " **Windows 身份验证凭据** " 属性，然后选择其 **省略号 ("**) " 按钮，弹出 " **windows 身份验证凭据** " 窗口。

   ![启用 Windows 身份验证](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. 输入您的 Windows 身份验证凭据。 例如，若要访问 Azure 文件，可以 `Azure` `YourStorageAccountName` `YourStorageAccountKey` 分别为 " **域**"、" **用户名**" 和 " **密码**" 输入、和。

   ![Windows 身份验证凭据](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>启动包执行

在将启用了 Azure 的项目连接到 ADF 中的 SSIS、评估其云兼容性并缓解潜在问题之后，你可以在 Azure-SSIS IR 上执行/测试包。
-  选择 SSDT 工具栏中的 " **启动** " 按钮以下拉菜单。 接下来，选择 " **在 Azure 中执行** " 项。

   ![在 Azure 中执行](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  或者，右键单击 SSDT 的 "解决方案资源管理器" 窗口中的 "包" 节点，弹出菜单。 接下来，选择 " **在 Azure 中执行包** " 项。

   ![在 Azure 中执行包](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> 若要在 Azure 中执行包，需要具有运行的 Azure-SSIS IR，因此，如果 Azure-SSIS IR 已停止，则会弹出一个对话框窗口以启动它。 此过程应在 5 分钟内完成（不包括任何自定义设置时间），但对于加入虚拟网络的 Azure-SSIS IR，此过程可能需要大约 20 - 30 分钟。 在 Azure 中执行包后，你可以通过在 SSDT 的 "解决方案资源管理器" 窗口中右键单击其节点来停止 Azure-SSIS IR 来管理其运行成本，以弹出一个菜单，然后选择将你转到 ADF 门户的 **Start\Stop\Manage** 项。

### <a name="using-execute-package-task"></a>使用执行包任务

如果包中包含的执行包任务引用存储在本地文件系统上的子包，请执行以下附加步骤：

1. 将子包上传到与你的项目连接的同一 Azure 存储帐户下的 Azure 文件中，并 (UNC) 路径获取其新的通用命名约定，例如 `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. 将执行包任务的文件连接管理器中这些子包的文件路径替换为新的 UNC 路径
   - 如果运行 SSDT 的本地计算机无法访问新 UNC 路径，可以在文件连接管理器的属性面板中输入该路径
   - 另外，还可以在运行时使用文件路径的变量来分配适当的值

如果包中包含引用同一项目中的子包的执行包任务，则无需执行其他步骤。

### <a name="switching-package-protection-level"></a>切换包保护级别

在 Azure 中执行 SSIS 包不支持**EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey**保护级别。 因此，如果将包配置为使用这些包，则会将它们分别转换为使用**EncryptSensitiveWithPassword** / **EncryptAllWithPassword**保护级别。 当我们将包上传到 Azure 文件中以执行 Azure-SSIS IR 的执行时，我们还会随机生成加密密码。

> [!NOTE]
> 如果包中包含的执行包任务引用配置为使用**EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey**保护级别的子包，则需要在执行包之前手动重新配置这些子包以使用**EncryptSensitiveWithPassword** / **EncryptAllWithPassword**保护级别。

如果你的包已配置为使用**EncryptSensitiveWithPassword** / **EncryptAllWithPassword**保护级别，我们会将其保持不变。 当我们将包上传到 Azure 文件中以执行 Azure-SSIS IR 的执行时，我们仍会随机生成加密密码。

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> 切换包执行环境

如果在项目部署模型中将项目/包参数化，则可以创建多个 VS 配置来切换包执行环境。 通过这种方式，可以在运行时将特定于环境的值分配给项目/包参数。 建议在本地和云环境中为包执行至少使用两个不同的 VS 配置，以便可以根据云配置启用项目。 下面是在本地计算机与 Azure 之间切换包执行环境的分步示例：

1. 假设你的包包含一个设置文件属性的文件系统任务。 当你在本地计算机上运行该文件时，它将设置存储在本地文件系统上的文件的属性。 当你在 Azure-SSIS IR 上运行它时，你希望它设置 Azure 文件中存储的文件的属性。 首先，创建一个字符串类型的包参数， **并将其命名为文件** 路径以保存目标文件路径的值。

   ![创建包参数](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. 接下来，在 "**文件系统任务编辑器**" 窗口的 "**常规**" 页上，用**FilePath**包参数参数化**源连接**部分中的**SourceVariable**属性。 

   ![参数化源连接](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. 默认情况下，在名为 " **开发**" 的本地环境中有一个现有的 VS 配置用于执行包。 在名为 **Azure**的云环境中为包执行创建新的 vs 配置，请参阅 [创建新的 vs 配置](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019)（如果尚未这样做）。

4. 查看包的参数时，请选择 " **将参数添加到配置** " 按钮，打开包的 " **管理参数值** " 窗口。 接下来，在**开发**和**Azure**配置下向**FilePath**包参数分配不同的目标文件路径值。

   ![分配参数值](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure-启用项目的云配置，请参阅 [Azure-启用现有 SSIS 项目](#azureenableproject)（如果尚未这样做）。 接下来，配置启用了 Azure 的设置以启用 Azure-SSIS IR 的 Windows 身份验证访问 Azure 文件，请参阅 [配置启用了 azure 的设置](#azureenabledsettings)（如果尚未这样做）。

6. 在 Azure 中执行包。 可以通过选择 " **开发** " 配置将包执行环境切换回本地计算机。

   ![切换 Visual Studio 配置](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>使用包配置文件

如果在包部署模型中使用包配置文件，则可以在运行时将特定于环境的值分配给包属性。 我们会自动将包中的这些文件上传到 Azure 文件中，以便在 Azure-SSIS IR 执行。

### <a name="checking-package-execution-logs"></a>检查包执行日志

在开始执行包后，我们将在 "SSDT" 的 " **进度** " 窗口中设置格式并显示其日志。 对于长时间运行的包，我们会在几分钟后定期更新其日志。 通过选择 SSDT 工具栏中的 " **停止** " 按钮，可以立即取消包执行。 你还可以在以下 UNC 路径中暂时查找其日志的原始数据： `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` ，但我们会在一天后将其清除。

## <a name="current-limitations"></a>当前限制

-  支持 Azure 的 SSDT 仅支持商业/全球云区域，目前不支持政府/国内云区域。

## <a name="next-steps"></a>后续步骤

在 Azure 中从 SSDT 运行包后，你可以在 ADF 管道中将其部署和运行为 "执行 SSIS 包" 活动，请参阅 [在 adf 管道中将 ssis 包作为 "执行 Ssis 包" 活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
