---
title: 通过启用了 Azure 的 dtexec 实用工具执行 SQL Server Integration Services （SSIS）包 |Microsoft Docs
description: 了解如何通过启用了 Azure 的 dtexec 实用程序执行 SQL Server Integration Services （SSIS）包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 472792351b8b7ab96e055bacd64141840ce7a630
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596947"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>通过启用了 Azure 的 dtexec 实用工具运行 SQL Server Integration Services 包
本文介绍了支持 Azure 的 dtexec （AzureDTExec）命令提示实用工具。 它用于在 Azure 数据工厂中的 Azure-SSIS Integration Runtime （IR）上运行 SQL Server Integration Services （SSIS）包。

传统的 dtexec 实用工具附带 SQL Server。 有关详细信息，请参阅[dtexec 实用工具](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 它通常由第三方协调器或计划程序调用，如 ActiveBatch 和控制-M，用于在本地运行 SSIS 包。 

新式 AzureDTExec 实用工具附带 SQL Server Management Studio （SSMS）工具。 它还可由第三方协调器或计划程序调用，以在 Azure 中运行 SSIS 包。 它有助于将 SSIS 包提升和转移或迁移到云。 迁移后，如果想要在日常操作中继续使用第三方协调器或计划程序，现在可以调用 AzureDTExec 而不是 dtexec。

AzureDTExec 在数据工厂管道中将包作为 "执行 SSIS 包" 活动运行。 有关详细信息，请参阅将[SSIS 包作为 Azure 数据工厂活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。 

可以通过 SSMS 配置 AzureDTExec，以使用在数据工厂中生成管道的 Azure Active Directory （Azure AD）应用程序。 还可将其配置为访问存储包的文件系统、文件共享或 Azure 文件。 根据为其调用选项提供的值，AzureDTExec 将生成并运行一个具有 "执行 SSIS 包" 活动的唯一数据工厂管道。 对其选项调用具有相同值的 AzureDTExec 会重新运行现有管道。

## <a name="prerequisites"></a>必备组件
若要使用 AzureDTExec，请下载并安装最新版本的 SSMS，它是版本18.3 或更高版本。 从此[网站](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下载。

## <a name="configure-the-azuredtexec-utility"></a>配置 AzureDTExec 实用工具
在本地计算机上安装 SSMS 还会安装 AzureDTExec。 若要配置其设置，请通过 "以**管理员身份运行**" 选项启动 SSMS。 然后选择 "**工具**"  > **迁移到 azure**  > **配置启用了 azure 的 DTExec**。

![配置启用了 Azure 的 dtexec 菜单](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

此操作将打开一个**AzureDTExecConfig**窗口，该窗口需要使用管理权限打开才能写入*AzureDTExec*文件。 如果未以管理员身份运行 SSMS，则将打开 "用户帐户控制（UAC）" 窗口。 输入管理员密码以提升权限。

![配置启用了 Azure 的 dtexec 设置](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

在 " **AzureDTExecConfig** " 窗口中，输入配置设置，如下所示：

- **ApplicationId**：输入你用适当的权限创建的 Azure AD 应用的唯一标识符，以在数据工厂中生成管道。 有关详细信息，请参阅[通过 Azure 门户创建 Azure AD 应用和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。
- **AuthenticationKey**：输入 Azure AD 应用的身份验证密钥。
- **TenantId**：输入在其下创建 Azure AD 应用的 Azure AD 租户的唯一标识符。
- **SubscriptionId**：输入创建数据工厂时所用的 Azure 订阅的唯一标识符。
- **资源**组：输入在其中创建数据工厂的 Azure 资源组的名称。
- **DataFactory**：输入你的数据工厂的名称，其中的 "执行 SSIS 包" 活动中包含 "执行 SSIS 包" 活动的唯一管道基于调用 AzureDTExec 时提供的选项值生成。
- **IRName**：在你的数据工厂中输入 Azure-SSIS IR 的名称，当你调用 AzureDTExec 时，在该名称中指定的包将在其通用命名约定（UNC）路径中运行。
- **PackageAccessDomain**：输入域凭据，以访问你在调用 AzureDTExec 时指定的 UNC 路径中的包。
- **PackageAccessUserName**：输入用户名凭据，以访问你在调用 AzureDTExec 时指定的 UNC 路径中的包。
- **PackageAccessPassword**：输入密码凭据，以访问在调用 AzureDTExec 时指定的包的 UNC 路径中的包。
- **LogPath**：输入日志文件夹的 UNC 路径，将在其中写入 Azure-SSIS IR 上包执行的日志文件。
- **LogLevel**：针对 Azure-SSIS IR 上的包执行，输入所选的日志记录范围，从预定义的**null**、**基本**、**详细**或**性能**选项。
- **LogAccessDomain**：当你编写日志文件时，请输入域凭据，以访问其 UNC 路径中的日志文件夹，当指定**LogPath**且**LogLevel**不**为 null**时，这是必需的。
- **LogAccessUserName**：当你编写日志文件时，请输入用户名凭据来访问其 UNC 路径中的日志文件夹，当指定**LogPath**并且**LogLevel**不**为 null**时，这是必需的。
- **LogAccessPassword**：当你编写日志文件时，请输入密码凭据以访问其 UNC 路径中的日志文件夹，当指定**LogPath**且**LogLevel**不**为 null**时，这是必需的。
- **PipelineNameHashStrLen**：输入要从在调用 AzureDTExec 时提供的选项值生成的哈希字符串的长度。 字符串用于构建 Azure-SSIS IR 上运行包的数据工厂管道的唯一名称。 通常，长度为32个字符。

若要将包和日志文件存储在本地文件系统或文件共享中，请将 Azure-SSIS IR 加入到连接到本地网络的虚拟网络，以便它可以提取包并写入日志文件。 有关详细信息，请参阅将[Azure-SSIS IR 加入到虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

为避免显示以纯文本形式写入*AzureDTExec*文件的敏感值，我们将它们编码为 Base64 编码的字符串。 调用 AzureDTExec 时，会将所有 Base64 编码的字符串解码回其原始值。 可以通过限制可访问的帐户来进一步保护*AzureDTExec*文件。

## <a name="invoke-the-azuredtexec-utility"></a>调用 AzureDTExec 实用工具
您可以在命令行提示符处调用 AzureDTExec，并为用例方案中的特定选项提供相关值。

实用工具安装在 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`。 可以将其路径添加到 "PATH" 环境变量中，以便从任何位置调用它。

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

调用 AzureDTExec 提供类似于调用 dtexec 的选项。 有关详细信息，请参阅[Dtexec 实用工具](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 以下是当前支持的选项：

- **/F [ile]** ：加载存储在文件系统、文件共享或 Azure 文件中的包。 作为此选项的值，你可以在文件系统、文件共享或 Azure 文件中为包文件指定 UNC 路径，扩展名为 .dtsx。 如果指定的 UNC 路径包含任何空格，请在整个路径的两侧加上引号。
- **/Conf [igFile]** ：指定要从中提取值的配置文件。 使用此选项，可以为包设置与设计时指定的配置不同的运行时配置。 您可以在 XML 配置文件中存储不同的设置，然后在执行包之前加载这些设置。 有关详细信息，请参阅[SSIS 包配置](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)。 若要指定此选项的值，请将文件系统、文件共享或 Azure 文件中的配置文件的 UNC 路径用于其 Datatransferconfig.dtsconfig 扩展。 如果指定的 UNC 路径包含任何空格，请在整个路径的两侧加上引号。
- **/Conn [ection]** ：为包中的现有连接管理器指定连接字符串。 使用此选项，可以为包中的现有连接管理器设置与在设计时指定的连接管理器不同的运行时连接字符串。 按如下所示为此选项指定值： `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`。
- **/Set**：重写包中参数、变量、属性、容器、日志提供程序、Foreach 枚举器或连接的配置。 可以多次指定此选项。 按如下所示为此选项指定值： `property_path;value`。 例如，`\package.variables[counter].Value;1` 将 `counter` 变量的值替换为1。 你可以使用**包配置**向导来查找、复制和粘贴包中要替代其值的项的 `property_path` 值。 有关详细信息，请参阅[包配置向导](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)。
- **/De [dm-crypt]** ：为包配置包含**EncryptAllWithPassword** /**EncryptSensitiveWithPassword**保护级别的解密密码。

> [!NOTE]
> 使用新的选项值调用 AzureDTExec 会生成新的管道，但选项 **/De [脚本]** 除外。

## <a name="next-steps"></a>后续步骤

在调用 AzureDTExec 之后，生成和运行具有 "执行 SSIS 包" 活动的唯一管道之后，可以在数据工厂门户中对其进行监视。 有关详细信息，请参阅将[SSIS 包作为数据工厂活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

> [!WARNING]
> 生成的管道应仅由 AzureDTExec 使用。 它的属性或参数将来可能会更改，因此请勿出于任何其他目的修改或重用它们。 修改可能会破坏 AzureDTExec。 如果发生这种情况，请删除管道。 AzureDTExec 将在下一次调用时生成新的管道。
