---
title: 使用启用 Azure 的 dtexec 实用程序执行 SQL 服务器集成服务 （SSIS） 包
description: 了解如何使用支持 Azure 的 dtexec 实用工具执行 SQL Server Integration Services (SSIS) 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: dce7fb87ee49aefdedf5653243fa5729eee34519
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414330"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>使用支持 Azure 的 dtexec 实用工具运行 SQL Server Integration Services 包

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍支持 Azure 的 dtexec (AzureDTExec) 命令提示符实用工具。 此实用工具用于在 Azure 数据工厂中的 Azure-SSIS Integration Runtime (IR) 上运行 SQL Server Integration Services (SSIS) 包。

SQL Server 随附了传统的 dtexec 实用工具。 有关详细信息，请参阅[dtexec 实用程序](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 此实用工具通常由 ActiveBatch 和 Control-M 等第三方业务流程协调程序或计划程序调用，以在本地运行 SSIS 包。 

SQL Server Management Studio (SSMS) 工具随附了新式 AzureDTExec 实用工具。 它也可以由第三方业务流程协调程序或计划程序调用，以在 Azure 中运行 SSIS 包。 此实用程序有助于将 SSIS 包直接转移或迁移到云中。 迁移后，如果你想要在日常运营中保持使用第三方业务流程协调程序或计划程序，这些程序现在可以调用 AzureDTExec 而不是 dtexec。

AzureDTExec 将包作为数据工厂管道中的“执行 SSIS 包”活动运行。 有关详细信息，请参阅[将 SSIS 包作为 Azure 数据工厂活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。 

可以通过 SSMS 将 AzureDTExec 配置为使用在数据工厂中生成管道的 Azure Active Directory (Azure AD) 应用程序。 还可将其配置为访问存储包的文件系统、文件共享或 Azure 文件存储。 根据提供给 AzureDTExec 的调用选项值，AzureDTExec 将生成并运行一个独特的数据工厂管道，其中包含“执行 SSIS 包”活动。 结合提供的相同选项值调用 AzureDTExec 会重新运行现有管道。

## <a name="prerequisites"></a>先决条件
若要使用 AzureDTExec，请下载并安装最新版本的 SSMS（18.3 或更高版本）。 从[此网站](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下载它。

## <a name="configure-the-azuredtexec-utility"></a>配置 AzureDTExec 实用工具
在本地计算机上安装 SSMS 会同时安装 AzureDTExec。 若要配置 AzureDTExec 的设置，请使用“以管理员身份运行”选项启动 SSMS。**** 然后选择 **"** > **工具迁移到 Azure** > **配置启用 Azure 的 DTExec"。**

![“配置支持 Azure 的 DTExec”菜单](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

如果使用管理特权执行此操作，将会打开一个“AzureDTExecConfig”窗口，并将设置写入 *AzureDTExec.settings* 文件中。**** 如果不是以管理员身份运行 SSMS，将打开“用户帐户控制(UAC)”窗口。 输入管理员密码以提升特权。

![“配置支持 Azure 的 DTExec”设置](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

在“AzureDTExecConfig”窗口中，按如下所示输入配置设置：****

- **应用程序 Id**：输入使用在数据工厂中生成管道的正确权限创建的 Azure AD 应用的唯一标识符。 有关详细信息，请参阅[通过 Azure 门户创建 Azure AD 应用和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。
- **身份验证密钥**：输入 Azure AD 应用的身份验证密钥。
- **租户 Id**：输入 Azure AD 租户的唯一标识符，在该标识符下创建 Azure AD 应用。
- **数据工厂**：输入数据工厂的名称，其中根据调用 AzureDTExec 时提供的选项值生成具有执行 SSIS 包活动的唯一管道。
- **IRName**： 在数据工厂中输入 Azure-SSIS IR 的名称，在调用 AzureDTExec 时，在其通用命名约定 （UNC） 路径中指定的包将在该名称上运行。
- **管道名称HashStrLen：** 输入从调用 AzureDTExec 时提供的选项值生成的哈希字符串的长度。 这些字符串用于构成在 Azure-SSIS IR 上运行包的数据工厂管道的唯一名称。 32 个字符的长度通常已足够。
- **资源组**：输入在其中创建数据工厂的 Azure 资源组的名称。
- **订阅 Id**：输入 Azure 订阅的唯一标识符，根据该标识符创建数据工厂。
- **LogAccessDomain**： 在写入日志文件时输入域凭据以访问其 UNC 路径中的日志文件夹，这在指定**LogPath**且**LogLevel**不**为 null**时是必需的。
- **LogAccess密码**：在写入日志文件时输入密码凭据以访问其 UNC 路径中的日志文件夹，这在指定**LogPath**且**LogLevel**不**为 null**时是必需的。
- **LogAccessUserName：** 在写入日志文件时输入用户名凭据以访问其 UNC 路径中的日志文件夹，这在指定**LogPath**且**LogLevel**不为**null**时是必需的。
- **日志级别**：输入 Azure-SSIS IR 上包执行的预定义**null、****基本**、**详细**或**性能**选项的选定日志记录范围。
- **LogPath**： 输入日志文件夹的 UNC 路径，其中将 Azure-SSIS IR 上的包执行中的日志文件写入该路径。
- **包 AccessDomain**： 输入域凭据以访问您在调用 AzureTExec 时指定的 UNC 路径中的包。
- **包访问密码**：输入密码凭据以访问调用 AzureDTExec 时指定的 UNC 路径中的包。
- **包访问用户名**：输入用户名凭据以访问您在调用 AzureDTExec 时指定的 UNC 路径中的包。

若要将包和日志文件存储在本地的文件系统或文件共享中，请将 Azure-SSIS IR 加入到与本地网络连接的虚拟网络，使 IR 能够提取包并写入日志文件。 有关详细信息，请参阅[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

若要避免以纯文本显示写入 *AzureDTExec.settings* 的敏感值，请将其编码为 Base64 编码的字符串。 调用 AzureDTExec 时，所有 Base64 编码的字符串将解码回到原始值。 可以通过限制能够访问 *AzureDTExec* 文件的帐户来进一步保护该文件。

## <a name="invoke-the-azuredtexec-utility"></a>调用 AzureDTExec 实用工具
可以在命令行提示符下调用 AzureDTExec，并为用例方案中的特定选项提供相关值。

该实用工具安装在 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` 中。 可将其路径添加到“PATH”环境变量，以便能够从任意位置调用它。

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

调用 AzureDTExec 时可用的选项类似于调用 dtexec 时可用的选项。 有关详细信息，请参阅 [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 目前支持以下选项：

- **/F[ile]**：加载存储在文件系统、文件共享或 Azure 文件中的包。 可以指定包文件在文件系统、文件共享或 Azure 文件存储中的 UNC 路径及其 .dtsx 扩展名，作为此选项的值。 如果指定的 UNC 路径包含空格，请用引号括住整个路径。
- **/Conf_igFile]**： 指定要从中提取值的配置文件。 使用此选项可为包设置不同的运行时配置（不同于设计时指定的配置）。 可以在 XML 配置文件中存储不同的设置，然后在执行包之前加载这些设置。 有关详细信息，请参阅 [SSIS 包配置](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)。 若要指定此选项的值，请使用配置文件在文件系统、文件共享或 Azure 文件存储中的 UNC 路径及其 dtsConfig 扩展名。 如果指定的 UNC 路径包含空格，请用引号括住整个路径。
- **/Conn_ection}**：为包中的现有连接管理器指定连接字符串。 使用此选项可为包中的现有连接管理器设置不同的运行时连接字符串（不同于设计时指定的连接字符串）。 按如下所示指定此选项的值：`connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`。
- **/Set**： 覆盖包中的参数、变量、属性、容器、日志提供程序、Foreach 枚举器或连接的配置。 可以多次指定此选项。 按如下所示指定此选项的值：`property_path;value`。 例如，`\package.variables[counter].Value;1` 将 `counter` 变量的值重写为 1。 可以使用“包配置”向导来查找、复制和粘贴包中要重写其值的项的 `property_path` 值。**** 有关详细信息，请参阅[包配置向导](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)。
- **/De[密码]**：为配置了**加密密码**/**加密**敏感密码保护级别的包设置解密密码。

> [!NOTE]
> 使用新选项值（**/De[cript]** 选项除外）调用 AzureDTExec 会生成新的管道。

## <a name="next-steps"></a>后续步骤

在调用 AzureDTExec 时生成并运行具有其中执行 SSIS 包活动的唯一管道后，可以在数据工厂门户上监视它们。 如果要使用数据工厂协调/计划数据工厂触发器，也可以为其分配数据工厂触发器。 有关详细信息，请参阅[将 SSIS 包作为数据工厂活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

> [!WARNING]
> 生成的管道预期仅由 AzureDTExec 使用。 其属性或参数将来可能会更改，因此请不要对其进行修改，或将其重复用于任何其他目的。 进行修改可能会破坏 AzureDTExec。 如果发生这种情况，请删除管道。 下一次调用 AzureDTExec 时，它会生成新的管道。