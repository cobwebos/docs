---
title: 通过启用了 Azure 的 dtexec 实用工具执行 SQL Server Integration Services （SSIS）包 |Microsoft Docs
description: 了解如何通过启用了 Azure 的 dtexec 实用工具执行 SQL Server Integration Services （SSIS）包。
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
ms.openlocfilehash: 80d4bd2f4f9ea4c451f312f05fd42fbc1ba433ab
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181322"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>通过启用了 Azure 的 dtexec 实用工具运行 SQL Server Integration Services （SSIS）包
本文介绍了支持 Azure 的**dtexec** （**AzureDTExec**）命令提示实用工具。  它用于在 Azure 数据工厂（ADF）中 Azure-SSIS Integration Runtime （IR）上运行 SSIS 包。

传统**dtexec**实用工具附带 SQL Server，有关详细信息，请参阅[dtexec 实用工具](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)文档。  它通常由第三方协调器/计划程序调用，如活动批处理、控制-M 等，以便在本地运行 SSIS 包。  新式**AzureDTExec**实用工具附带 SQL SERVER MANAGEMENT STUDIO （SSMS）工具。  它还可由第三方协调器/计划程序调用，以在 Azure 中运行 SSIS 包。  它简化了将 SSIS 包转移/迁移到云 & 的提升。  迁移后，如果想要在日常操作中继续使用第三方协调器/计划程序，现在可以调用**AzureDTExec**而不是**dtexec**。

**AzureDTExec**将在 adf 管道中将包作为 "执行 ssis 包" 活动运行，有关详细信息，请参阅将[SSIS 包作为 adf 活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)一文。  可以通过 SSMS 对其进行配置，以使用在 ADF 中生成管道的 Azure Active Directory （AAD）应用程序。  还可将其配置为访问存储包的文件系统/文件共享/Azure 文件。  根据为其调用选项所赋的值， **AzureDTExec**将生成并运行包含 "执行 SSIS 包" 活动的唯一 ADF 管道。  调用具有相同选项值的**AzureDTExec**会重新运行现有管道。  使用新值为其选项调用**AzureDTExec**将生成新的管道。

## <a name="prerequisites"></a>先决条件
若要使用**AzureDTExec**，请从[此处](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下载并安装最新的 SSMS （版本18.3 或更高版本）。

## <a name="configure-azuredtexec-utility"></a>配置 AzureDTExec 实用工具
在本地计算机上安装 SSMS 还会安装**AzureDTExec**。  若要配置其设置，请以 "以**管理员身份运行**" 选项启动 SSMS，然后选择级联下拉菜单菜单项**工具-> 迁移到 Azure-> 配置启用了 azure 的 DTExec**。

![配置启用了 Azure 的 dtexec 菜单](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

此操作将弹出**AzureDTExecConfig**窗口，该窗口需要用管理权限打开才能写入**AzureDTExec**文件。  如果你未以管理员身份运行 SSMS，则会弹出一个 "用户帐户控制（UAC）" 窗口，提示你输入管理员密码，以提升你的权限。

![配置启用了 Azure 的 dtexec 设置](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

在**AzureDTExecConfig**窗口中，可以按如下所示输入配置设置：

- **ApplicationId**：输入使用正确的权限创建的 AAD 应用的唯一标识符，以在 ADF 中生成管道。有关详细信息，请参阅[通过 Azure 门户文章创建 aad 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

- **AuthenticationKey**：输入 AAD 应用的身份验证密钥。

- **TenantId**：输入 aad 租户的唯一标识符，该标识符在其下创建 AAD 应用。

- **SubscriptionId**：输入在其下创建 ADF 的 Azure 订阅的唯一标识符。

- **ResourceGroup**：输入创建 ADF 的 Azure 资源组的名称。

- **DataFactory**：输入 ADF 的名称，其中的 "执行 SSIS 包" 活动中包含 "执行 SSIS 包" 活动的唯一管道是根据调用**AzureDTExec**时提供的选项值生成的。

- **IRName**：在 ADF 中输入 Azure-SSIS IR 的名称，当调用**AzureDTExec**时，将在其通用命名约定（UNC）路径中指定包。

- **PackageAccessDomain**：输入域凭据，以访问在调用**AzureDTExec**时指定的 UNC 路径中的包。

- **PackageAccessUserName**：输入用户名凭据，以在调用**AzureDTExec**时指定其 UNC 路径访问包。

- **PackageAccessPassword**：输入密码凭据，以在调用**AzureDTExec**时指定其 UNC 路径中的包。

- **LogPath**：输入日志文件夹的 UNC 路径，将在其中写入 Azure-SSIS IR 包执行的日志文件。

- **LogLevel**：为 Azure-SSIS IR 上的包执行输入从预定义的**null**/**基本**/**详细**/**性能**选项中选择的日志记录范围。

- **LogAccessDomain**：写入日志文件时，请输入域凭据，以访问其 UNC 路径中的日志文件夹，当指定**LogPath**并且**LogLevel**不为**null**时，需要使用此凭据。

- **LogAccessUserName**：输入 "用户名" 凭据，以在写入日志文件时访问其 UNC 路径中的日志文件夹，当指定**LogPath**时， **LogLevel**不为**null**。

- **LogAccessPassword**：输入密码凭据以便在写入日志文件时访问其 UNC 路径中的日志文件夹，当指定了**LogPath**且**LogLevel**不为**null**时，需要使用该凭据。

- **PipelineNameHashStrLen**：输入在调用**AzureDTExec**时提供的选项值所生成的哈希字符串的长度。  这些字符串将用于为在 Azure-SSIS IR 上运行包的 ADF 管道建立唯一名称。  长度为32个字符就足够了。

如果计划将包和日志文件存储在本地的文件系统/文件共享中，则应将 Azure-SSIS IR 加入到连接到本地网络的 VNet，以便可以提取包并写入日志文件，请参阅[将 Azure-SSIS IR 加入到 VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)有关详细信息的文章。

为避免显示以纯文本形式写入**AzureDTExec**文件的敏感值，我们会将它们编码为 Base64 编码的字符串。  调用**AzureDTExec**时，所有 Base64 编码的字符串都将解码回其原始值。  可以通过限制可访问的帐户来进一步保护**AzureDTExec**文件。

## <a name="invoke-azuredtexec-utility"></a>调用 AzureDTExec 实用工具
您可以在命令行提示符处调用**AzureDTExec** ，并为用例方案中的特定选项提供相关的值，例如：

`AzureDTExec.exe
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"
  /Set \package.variables[MyVariable].Value;MyValue
  /De MyEncryptionPassword`

调用**AzureDTExec**提供类似于调用**dtexec**的选项，有关详细信息，请参阅[dtexec 实用工具](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)文档。  以下是当前支持的选项：

- **/F [ile]** ：加载存储在文件系统/文件共享/Azure 文件中的包。  作为此选项的值，你可以在文件系统/文件共享/Azure 文件中为包文件指定其 .dtsx 扩展名的 UNC 路径。  如果指定的 UNC 路径包含任意空间，则必须在整个路径两侧加上引号。

- **/Conf [igFile]** ：指定要从中提取值的配置文件。  使用此选项，可以为包设置与设计时指定的配置不同的运行时配置。  您可以在 XML 配置文件中存储不同的设置，然后在执行包之前加载这些设置。  有关详细信息，请参阅[SSIS 包配置](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)一文。  作为此选项的值，你可以在文件系统/文件共享/Azure 文件中指定配置文件的 UNC 路径，其 Datatransferconfig.dtsconfig 扩展。  如果指定的 UNC 路径包含任意空间，则必须在整个路径两侧加上引号。

- **/Conn [ection]** ：为包中的现有连接管理器指定连接字符串。  使用此选项，可以为包中的现有连接管理器设置与在设计时指定的连接管理器不同的运行时连接字符串。  作为此选项的值，可以按如下所示指定它： `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`。

- **/Set**：重写包中参数、变量、属性、容器、日志提供程序、Foreach 枚举器或连接的配置。  可以多次指定此选项。  作为此选项的值，您可以按如下所示指定它`property_path;value`：。例如`\package.variables[counter].Value;1` ，将`counter`变量的值重写为1。  你可以使用包配置向导来查找、复制和粘贴包中要替代`property_path`其值的项的值，有关详细信息，请参阅[包配置向导](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)文档。

- **/De [dm-crypt]** ：设置用**EncryptAllWithPassword**/**EncryptSensitiveWithPassword**保护级别配置的包的解密密码。

## <a name="next-steps"></a>后续步骤
一旦调用**AzureDTExec**后，将生成并运行其上包含 "执行 SSIS 包" 活动的唯一管道，可以在 adf 门户中编辑和或重新运行它们，有关详细信息，请参阅将[SSIS 包作为 ADF 活动运行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)一文。
