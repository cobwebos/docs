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
ms.date: 09/29/2020
ms.openlocfilehash: 158adb6b35b488c310bd2912d4076b86579383a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446408"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>使用 Azure-SSIS Integration Runtime 包存储来管理包

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

若要将本地 SQL Server Integration Services (SSIS) 工作负荷直接迁移到云，可以在 Azure 数据工厂 (ADF) 中预配 Azure-SSIS Integration Runtime (IR)。 有关详细信息，请参阅[预配 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)。 Azure-SSIS IR 支持：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

使用包部署模型时，可以选择是否要使用包存储来预配 Azure-SSIS IR。 它们在文件系统、Azure 文件或 Azure SQL 托管实例托管的 MSDB 之上提供了包管理层。 Azure-SSIS IR 包存储允许你通过 SQL Server Management Studio (SSMS) 导入/导出/删除/运行包以及监视/停止正在运行的包，类似于[旧版 SSIS 包存储](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)。 

## <a name="connect-to-azure-ssis-ir"></a>连接到 Azure-SSIS IR

预配 Azure-SSIS IR 后，你可以连接到它来浏览其在 SSMS 上的包存储。

![连接到 Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

在 SSMS 的“对象资源管理器”窗口的“连接”下拉菜单中选择“Azure-SSIS Integration Runtime”。  接下来，登录到 Azure 并选择已预配包存储的相关订阅、ADF 和 Azure-SSIS IR。 你的 Azure-SSIS IR 会显示，它包含下面的“正在运行的包”和“已存储的包”节点。 展开“已存储的包”节点，查看下面的包存储。 展开包存储，查看下面的文件夹和包。 如果 SSMS 无法自动连接到包存储，系统可能会要求你输入其访问凭据。 例如，如果你展开 MSDB 顶部的一个包存储，系统可能会要求你先连接到 Azure SQL 托管实例。

![连接到 Azure SQL 托管实例](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>管理文件夹和包

连接到 SSMS 上的 Azure-SSIS IR 后，可以右键单击任何包存储、文件夹或包以弹出菜单，并选择“新文件夹”、“导入包”、“导出包”、“删除”或“刷新”    。

   ![管理文件夹和包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  选择“新建文件夹”可为导入的包创建一个新文件夹。

   *  选择“导入包”可将包从“文件系统”、“SQL Server (MSDB)”或旧版“SSIS 包存储”导入到你的包存储中。

      ![导入包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      根据要从中进行导入的“包位置”，选择相关的“服务器”/“身份验证类型”，在必要时输入访问凭据，选择“包路径”并输入新的“包名称”。 导入包时，无法更改其保护级别。 若要更改它，请使用 SQL Server Data Tools (SSDT) 或 `dtutil` 命令行实用工具。

      > [!NOTE]
      > 将 SSIS 包导入 Azure-SSIS IR 包存储时只能逐个完成，且只需将这些包复制到基础 MSDB/文件系统/Azure 文件中，同时保留其 SQL Server/SSIS 版本。 
      >
      > 由于 Azure-SSIS IR 当前基于 SQL Server 2017，因此在其上执行较低版本的包时，会在运行时将其升级为 SSIS 2017 包。 不支持执行更高版本的包。
      >
      > 此外，由于旧 SSIS 包存储绑定到特定的 SQL Server 版本并仅在 SSMS 上可访问该版本，因此需要先使用指定的 SSMS 版本将旧 SSIS 包存储中较低版本的包导出到文件系统中，然后才能使用 SSMS 2019 或更高版本将其导入 Azure-SSIS IR 包存储中。
      >
      > 或者，若要在切换 SSIS 包的保护级别时将多个 SSIS 包导入 Azure-SSIS IR 包存储，可以使用 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 命令行实用工具，请参阅[使用 dtutil 部署多个包](#deploying-multiple-packages-with-dtutil)。

   *  选择“导出包”可将包从你的包存储导出到“文件系统”、“SQL Server (MSDB)”或旧版“SSIS 包存储”中。

      ![导出包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      根据要导入到的“包位置”，选择相关的“服务器”/“身份验证类型”，在必要时输入访问凭据，并选择“包路径”。 导出包时，如果包已加密，请先输入密码对其进行解密，然后可以更改其保护级别，例如，更改为不存储任何敏感数据，或使用用户密钥或密码对敏感数据或所有数据进行加密。

      > [!NOTE]
      > 从 Azure-SSIS IR 包存储导出 SSIS 包时只能逐个完成，并且在不切换其保护级别的情况下执行此操作只需复制这些包，同时保留其 SQL Server/SSIS 版本，否则需将其升级到 SSIS 2019 或更高版本的包。
      >
      > 由于 Azure-SSIS IR 当前基于 SQL Server 2017，因此在其上执行较低版本的包时，会在运行时将其升级为 SSIS 2017 包。 不支持执行更高版本的包。
      >
      > 或者，若要在切换其保护级别时从 Azure-SSIS IR 包存储中导出多个 SSIS 包，可以使用 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 命令行实用工具，请参阅[使用 dtutil 部署多个包](#deploying-multiple-packages-with-dtutil)。

   *  选择“删除”可从包存储中删除现有文件夹/包。

   *  选择“刷新”可显示包存储中新添加的文件夹/包。

## <a name="execute-packages"></a>执行包

连接到 SSMS 上的 Azure-SSIS IR 后，可以右键单击任何存储的包以弹出菜单，并选择“运行包”。  这将打开“执行包实用工具”对话框，你可以在其中将 Azure-SSIS IR 上的包执行操作配置为 ADF 管道中的“执行 SSIS 包”活动。

![执行包实用工具页面 1 和 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![执行包实用工具页面 3 和 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

“执行包实用工具”对话框的“常规”、“配置”、“执行选项”和“日志记录”页面与“执行 SSIS 包”活动的“设置”选项卡相对应     。 在这些页面上，可以输入包的加密密码，并访问包配置文件的信息。 还可以输入包执行凭据和属性，以及日志文件夹的访问信息。  “执行包实用工具”对话框的“设置值”页面对应于“执行 SSIS 包”活动的“属性替代”选项卡，你可以在其中输入要替代的现有包属性。 有关详细信息，请参阅[将 SSIS 包作为 ADF 管道中的“执行 SSIS 包”活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

选择“执行”按钮时，将自动生成并触发包含“执行 SSIS 包”活动的新 ADF 管道。 如果已存在具有相同设置的 ADF 管道，则会重新运行该管道，并且不会生成新的管道。 ADF 管道和“执行 SSIS 包”活动将分别命名为 `Pipeline_SSMS_YourPackageName_HashString` 和 `Activity_SSMS_YourPackageName`。

![“执行包实用工具”按钮](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![执行 SSIS 包活动](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>监视和停止正在运行的包

连接到 SSMS 上的 Azure-SSIS IR 后，可以展开“正在运行的包”节点，以查看当前正在运行的包。  右键单击其中任何一个包以弹出菜单，然后选择“停止”或“刷新”。

   ![监视和停止正在运行的包](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  选择“停止”可取消将包作为“执行 SSIS 包”活动运行的当前正在运行的 ADF 管道。

   *  选择“刷新”可显示包存储中新的正在运行的包。

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>监视 Azure-SSIS IR 和编辑包存储

连接到 SSMS 上的 Azure-SSIS IR 后，可以右键单击它以弹出菜单，并选择“转到 Azure 数据工厂门户”或“刷新” 。

   ![转到 ADF 门户](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  选择“转到 Azure 数据工厂门户”以打开 ADF 监视中心的“集成运行时”页面，你可以在其中监视 Azure-SSIS IR。 在“包存储”磁贴上，可以看到附加到 Azure-SSIS IR 的包存储的数量。  选择该数字会弹出一个窗口，你可在其中编辑存储着包存储访问信息的 ADF 链接服务。

      ![编辑包存储](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  选择“刷新”可显示包存储中新添加的文件夹/包，以及包存储中正在运行的包。

## <a name="deploying-multiple-packages-with-dtutil"></a>使用 dtutil 部署多个包

若要将本地 SSIS 工作负载提升并转移到 ADF 中的 SSIS 上，同时保持旧包部署模型，需要将包从文件系统、SQL Server 托管的 MSDB 或旧 SSIS 包存储部署到 Azure 文件存储、Azure SQL 托管实例托管的 MSDB 或 Azure-SSIS IR 包存储中。 同时，还应将其保护级别从用户密钥加密更改为未加密或密码加密（如果尚未这样做）。

可以使用 SQL Server/SSIS 安装附带的 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 命令行实用工具来成批部署多个包。 该实用工具绑定到特定的 SSIS 版本，因此，如果使用该实用工具来部署较低版本的包而不切换其保护级别，则只需复制这些包并保留其 SSIS 版本。 如果使用该实用工具来部署这些包并同时切换其保护级别，则该工具会将这些包升级到其 SSIS 版本。

 由于 Azure-SSIS IR 当前基于 SQL Server 2017，因此在其上执行较低版本的包时，会在运行时将其升级为 SSIS 2017 包。 不支持执行更高版本的包。

因此，为避免运行时升级，在包部署模型中部署要在 Azure-SSIS IR 上运行的包时，应使用 SQL Server/SSIS 2017 安装附带的 dtutil 2017。 为此，可以下载并安装免费的 [SQL Server/SSIS 2017 开发人员版](https://go.microsoft.com/fwlink/?linkid=853016)。 安装后，可以在以下文件夹中找到 dtutil 2017：`YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn`。

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>使用 dtutil 将多个包从本地文件系统部署到 Azure 文件存储

 若要将多个包从文件系统部署到 Azure 文件存储并同时切换其保护级别，可以在命令提示符处运行以下命令。 请替换特定于你的情况的所有字符串。
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

若要在批处理文件中运行上述命令，请将 `%f` 替换为 `%%f`。

若要将来自文件系统顶部的旧 SSIS 包存储中的多个包部署到 Azure 文件存储并同时切换其保护级别，可以使用相同的命令，但请将 `YourLocalDrive:\...\YourPackageFolder` 替换为旧 SSIS 包存储使用的本地文件夹：`YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder`。 例如，如果旧 SSIS 包存储绑定到 SQL Server 2016，请转到 `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder`。  可以从 [SQL Server 默认兼容级别的列表](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#arguments)中找到 `YourSQLServerDefaultCompatibilityLevel` 的值。

如果已在 Azure 文件存储顶部配置 Azure-SSIS IR 包存储，则在连接到 SSMS 2019 或更高版本上的 Azure-SSIS IR 时，已部署的包将显示在其中。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>使用 dtutil 将本地 MSDB 中的多个包部署到 Azure 中的 MSDB

 若要将多个包从 SQL Server 托管的 MSDB 或 MSDB 顶部的旧 SSIS 包存储部署到 Azure SQL 托管实例托管的 MSDB 中，并同时切换其保护级别，可以连接到 SSMS 上的 SQL Server，右键单击 SSMS“对象资源管理器”上的 `Databases->System Databases->msdb` 节点，以打开“新查询”窗口，并运行以下 T-SQL 脚本 。 请替换特定于你的情况的所有字符串：  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

若要使用 Azure SQL 托管实例的专用/公共终结点，请 `YourSQLManagedInstanceEndpoint` 分别替换为 `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` 。

该脚本将为 MSDB 中的所有包生成 dtutil 命令行，你可以对其进行多重选择、复制和粘贴，然后在命令提示符下运行。

![生成 dtutil 命令行](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

如果已在 MSDB 顶部配置 Azure-SSIS IR 包存储，则在连接到 SSMS 2019 或更高版本上的 Azure-SSIS IR 时，已部署的包将显示在其中。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>使用 dtutil 将多个包从本地 MSDB 部署到 Azure 文件存储

 若要将多个包从 SQL Server 托管的 MSDB 或 MSDB 顶部的旧 SSIS 包存储部署到 Azure 文件存储中，并同时切换其保护级别，可以连接到 SSMS 上的 SQL Server，右键单击 SSMS“对象资源管理器”上的 `Databases->System Databases->msdb` 节点，以打开“新查询”窗口，并运行以下 T-SQL 脚本 。 请替换特定于你的情况的所有字符串：  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

该脚本将为 MSDB 中的所有包生成 dtutil 命令行，你可以对其进行多重选择、复制和粘贴，然后在命令提示符下运行。

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

如果已在 Azure 文件存储顶部配置 Azure-SSIS IR 包存储，则在连接到 SSMS 2019 或更高版本上的 Azure-SSIS IR 时，已部署的包将显示在其中。

## <a name="next-steps"></a>后续步骤

你可以重新运行/编辑自动生成的包含“执行 SSIS 包”活动的 ADF 管道，也可以在 ADF 门户上创建新管道。 有关详细信息，请参阅[将 SSIS 包作为 ADF 管道中的“执行 SSIS 包”活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
