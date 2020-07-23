---
title: 使用 Azure-SSIS Integration Runtime 包存储来管理包
description: 了解如何使用 Azure-SSIS Integration Runtime 包存储来管理包。
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
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84198864"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>使用 Azure-SSIS Integration Runtime 包存储来管理包

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

若要将本地 SQL Server Integration Services (SSIS) 工作负荷直接迁移到云，可以在 Azure 数据工厂 (ADF) 中预配 Azure-SSIS Integration Runtime (IR)。 有关详细信息，请参阅[预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)。 Azure-SSIS IR 支持：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

当使用包部署模型时，可以选择是否要为 Azure-SSIS IR 预配在由 Azure SQL 托管实例承载的文件系统/Azure 文件存储/MSDB 的基础上提供包管理层的包存储。 Azure-SSIS IR 包存储允许你通过 SQL Server Management Studio (SSMS) 导入/导出/删除/运行包以及监视/停止正在运行的包，类似于[旧版 SSIS 包存储](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)。 

## <a name="connect-to-azure-ssis-ir"></a>连接到 Azure-SSIS IR

预配 Azure-SSIS IR 后，你可以连接到它来浏览其在 SSMS 上的包存储。

![连接到 Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

在 SSMS 的“对象资源管理器”窗口的“连接”下拉菜单中选择“Azure-SSIS Integration Runtime”。  接下来，登录到 Azure 并选择你的包存储附加到的相关订阅、ADF 和 Azure-SSIS IR。 你的 Azure-SSIS IR 会显示，它包含下面的“正在运行的包”和“已存储的包”节点。 展开“已存储的包”节点，查看下面的包存储。 展开包存储，查看下面的文件夹和包。 如果 SSMS 无法自动连接到包存储，系统可能会要求你输入其访问凭据。 例如，如果你展开 MSDB 顶部的一个包存储，系统可能会要求你先连接到 Azure SQL 托管实例。

![连接到 Azure SQL 托管实例](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>管理文件夹和包

在 SSMS 上浏览 Azure-SSIS IR 时，可以右键单击任何包存储/文件夹/包以弹出一个菜单，然后选择“新建文件夹”、“导入包”、“导出包”、“删除”或“刷新”。

   ![管理文件夹和包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  选择“新建文件夹”可为导入的包创建一个新文件夹。

   *  选择“导入包”可将包从“文件系统”、“SQL Server (MSDB)”或旧版“SSIS 包存储”导入到你的包存储中。

      ![导入包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      根据要从中进行导入的“包位置”，选择相关的“服务器”/“身份验证类型”，在必要时输入访问凭据，选择“包路径”并输入新的“包名称”。 导入包时，无法更改其保护级别。 若要更改它，请使用 SQL Server Data Tools (SSDT) 或 `dtutil` 命令行实用工具。

   *  选择“导出包”可将包从你的包存储导出到“文件系统”、“SQL Server (MSDB)”或旧版“SSIS 包存储”中。

      ![导出包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      根据要导入到的“包位置”，选择相关的“服务器”/“身份验证类型”，在必要时输入访问凭据，并选择“包路径”。 导出包时，如果它们已加密，请先输入密码对其进行解密，然后可以更改其保护级别，例如，更改为不保存敏感数据或使用用户密钥/密码对所有数据/敏感数据进行加密。

   *  选择“删除”可从包存储中删除现有文件夹/包。

   *  选择“刷新”可显示包存储中新添加的文件夹/包。

## <a name="execute-packages"></a>执行包

在 SSMS 上浏览 Azure-SSIS IR 时，可以右键单击任何已存储的包以弹出菜单并选择“运行包”。  这将打开“执行包实用工具”对话框，你可以在其中将 Azure-SSIS IR 上的包执行操作配置为 ADF 管道中的“执行 SSIS 包”活动。

![执行包实用工具页面 1 和 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![执行包实用工具页面 3 和 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

“执行包实用工具”对话框的“常规”、“配置”、“执行选项”和“日志记录”页面对应于“执行 SSIS 包”活动的“设置”选项卡，你可以在其中输入包的加密密码、包配置文件的访问信息、包执行凭据/属性，以及日志文件夹的访问信息。  “执行包实用工具”对话框的“设置值”页面对应于“执行 SSIS 包”活动的“属性替代”选项卡，你可以在其中输入要替代的现有包属性。 有关详细信息，请参阅[将 SSIS 包作为 ADF 管道中的“执行 SSIS 包”活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

当选择“执行包实用工具”对话框的“执行”按钮时，会自动生成并触发包含“执行 SSIS 包”活动的一个新的 ADF 管道。 如果已存在具有相同包执行设置的一个 ADF 管道，则会重新运行该管道，并且不会生成新的管道。 ADF 管道和“执行 SSIS 包”活动将分别命名为 `Pipeline_SSMS_YourPackageName_HashString` 和 `Activity_SSMS_YourPackageName`。

![“执行包实用工具”按钮](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![执行 SSIS 包活动](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>监视和停止正在运行的包

在 SSMS 上浏览 Azure-SSIS IR 时，可以展开“正在运行的包”节点，以查看下面的当前正在运行的包。  右键单击其中任何一个包以弹出菜单，然后选择“停止”或“刷新”。

   ![监视和停止正在运行的包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  选择“停止”可取消将包作为“执行 SSIS 包”活动运行的当前正在运行的 ADF 管道。

   *  选择“刷新”可显示包存储中新的正在运行的包。

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>监视 Azure-SSIS IR 和编辑包存储

在 SSMS 上浏览 Azure-SSIS IR 时，可以右键单击它以弹出菜单并选择“转到 Azure 数据工厂门户”或“刷新”。

   ![转到 ADF 门户](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  选择“转到 Azure 数据工厂门户”以打开 ADF 监视中心的“集成运行时”页面，你可以在其中监视 Azure-SSIS IR。 在“包存储”磁贴上，可以看到附加到 Azure-SSIS IR 的包存储的数量。  选择该数字会弹出一个窗口，你可在其中编辑存储着包存储访问信息的 ADF 链接服务。

      ![编辑包存储](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  选择“刷新”可显示包存储中新添加的文件夹/包，以及包存储中正在运行的包。

## <a name="next-steps"></a>后续步骤

你可以重新运行/编辑自动生成的包含“执行 SSIS 包”活动的 ADF 管道，也可以在 ADF 门户上创建新管道。 有关详细信息，请参阅[将 SSIS 包作为 ADF 管道中的“执行 SSIS 包”活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。