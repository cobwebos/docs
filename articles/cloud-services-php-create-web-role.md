---
title: 为 PHP 创建 Azure Web 角色和辅助角色
description: 有关如何在 Azure 云服务中创建 PHP Web 和辅助角色以及配置 PHP 运行时的指南。
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297248"
---
# <a name="create-php-web-and-worker-roles"></a>创建 PHP Web 角色和辅助角色

## <a name="overview"></a>概述

本指南将说明如何执行以下操作：在 Windows 开发环境中创建 PHP Web 角色或辅助角色，从提供的“内置”版本中选择特定版本的 PHP，更改 PHP 配置，启用扩展，最后部署到 Azure。 它还介绍了如何将 Web 角色或辅助角色配置为使用提供的 PHP 运行时（带自定义配置和扩展）。

Azure 提供了三种计算模型以运行应用程序：Azure 应用服务、Azure 虚拟机和 Azure 云服务。 这三种模型都支持 PHP。 云服务（包括 Web 角色和辅助角色）提供了*平台即服务 (PaaS)* 。 在云服务中，Web 角色提供专门用于托管前端 Web 应用程序的 Internet Information Services (IIS) Web 服务器。 辅助角色可运行独立于用户交互或输入的异步任务、运行时间较长的任务或永久性任务。

有关这些选项的详细信息，请参阅 [Azure 提供的计算托管选项](cloud-services/cloud-services-choose-me.md)。

## <a name="download-the-azure-sdk-for-php"></a>下载 Azure SDK for PHP

[用于 PHP 的 Azure SDK](https://github.com/Azure/azure-sdk-for-php) 由多个组件构成。 本文将使用其中两个：Azure PowerShell 和 Azure 模拟器。 可以通过 Microsoft Web 平台安装程序安装这两个组件。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a name="create-a-cloud-services-project"></a>创建云服务项目

创建 PHP Web 角色和辅助角色的第一步是创建 Azure 服务项目。 Azure 服务项目用作 Web 角色和辅助角色的逻辑容器，包含项目的[服务定义 (.csdef)] 和[服务配置 (.cscfg)] 文件。

若要创建新的 Azure 服务项目，请以管理员身份运行 Azure PowerShell 并执行以下命令：

    PS C:\>New-AzureServiceProject myProject

此命令将创建可将 Web 角色和辅助角色添加到的新目录 (`myProject`)。

## <a name="add-php-web-or-worker-roles"></a>添加 PHP Web 角色或辅助角色

要将 PHP Web 角色添加到项目，请从项目的根目录中运行以下命令：

    PS C:\myProject> Add-AzurePHPWebRole roleName

对于辅助角色，请使用此命令：

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` 参数是可选的。 如果省略该参数，则会自动生成角色名称。 创建的第一个 Web 角色将为 `WebRole1`，第二个 Web 角色为 `WebRole2`，依此类推。 创建的第一个辅助角色将为 `WorkerRole1`，第二个辅助角色为 `WorkerRole2`，依此类推。
>
>

## <a name="use-your-own-php-runtime"></a>使用自己的 PHP 运行时

在某些情况下，可能需要提供自己的 PHP 运行时，而不是如上所述那样选择并配置内置 PHP 运行时。 例如，可以在 Web 角色或辅助角色中使用在开发环境中使用的 PHP 运行时， 以便更轻松地确保应用程序不会更改生产环境中的行为。

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>将 Web 角色配置为使用自己的 PHP 运行时

要将 Web 角色配置为使用提供的 PHP 运行时，请执行下列步骤：

1. 创建一个 Azure 服务项目并添加 PHP Web 角色，如本主题前面所述。
2. 在位于 Web 角色的根目录中的 `php` 文件夹中创建一个 `bin` 文件夹，然后将 PHP 运行时（所有二进制文件、配置文件、子文件夹等）添加到该 `php` 文件夹中。
3. 可有可无如果你的 PHP 运行时使用[Microsoft 用于 PHP 的驱动程序来 SQL Server][sqlsrv drivers]，则需要将你的 web 角色配置为在预配时安装[SQL Server Native Client 2012][sql native client] 。 为此，将 [sqlncli.msi x64 安装程序]添加到 Web 角色的根目录中的 `bin` 文件夹。 下一步中所述的启动脚本会在设置角色时以静默方式运行安装程序。 如果 PHP 运行时不使用 Microsoft Drivers for PHP for SQL Server，则可从下一步所示的脚本中删除以下行：

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. 定义一个启动任务，该任务将[Internet Information Services （IIS）][iis.net]配置为使用 PHP 运行时来处理 `.php` 页面的请求。 为此，请在文本编辑器中打开 `setup_web.cmd` 文件（位于 Web 角色的根目录的 `bin` 文件夹中），并将其内容替换为以下脚本：

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. 将应用程序文件添加到 Web 角色的根目录。 这会是 Web 服务器的根目录。
6. 按照以下[发布应用程序](#publish-your-application)部分中所述发布应用程序。

> [!NOTE]
> 在按照上述使用自己的 PHP 运行时的步骤进行操作后，可以删除 `download.ps1` 脚本（位于 Web 角色的根目录的 `bin` 文件夹中）。
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>将辅助角色配置为使用自己的 PHP 运行时

要将辅助角色配置为使用提供的 PHP 运行时，请执行下列步骤：

1. 创建一个 Azure 服务项目并添加 PHP 辅助角色，如本主题前面所述。
2. 在辅助角色的根目录中创建一个 `php` 文件夹，然后将 PHP 运行时（所有二进制文件、配置文件、子文件夹等）添加到该 `php` 文件夹中。
3. 可有可无如果 PHP 运行时使用[Microsoft 驱动程序用于 PHP SQL Server][sqlsrv drivers]，则需要将辅助角色配置为在预配[SQL Server Native Client 2012][sql native client]时安装。 为此，请将 [sqlncli.msi x64 安装程序]添加到辅助角色的根目录。 下一步中所述的启动脚本会在设置角色时以静默方式运行安装程序。 如果 PHP 运行时不使用 Microsoft Drivers for PHP for SQL Server，则可从下一步所示的脚本中删除以下行：

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. 定义在设置角色时将 `php.exe` 可执行文件添加到辅助角色的 PATH 环境变量中的启动任务。 为此，请在文本编辑器中打开 `setup_worker.cmd` 文件（位于辅助角色的根目录中），并将其内容替换为以下脚本：

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. 将应用程序文件添加到辅助角色的根目录。
6. 按照以下[发布应用程序](#publish-your-application)部分中所述发布应用程序。

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>在计算和存储模拟器中运行应用程序

Azure 模拟器提供了一个本地环境，可在将 Azure 应用程序部署到云之前在该本地环境中测试此应用程序。 模拟器与 Azure 环境之间存在一些差异。 若要更好地了解该模拟器，请参阅[使用 Azure 存储模拟器进行开发和测试](storage/common/storage-use-emulator.md)。

请注意，必须本地安装 PHP 才能使用计算模拟器。 计算模拟器将使用本地 PHP 安装来运行应用程序。

要在模拟器中运行项目，请从项目的根目录中执行以下命令：

    PS C:\MyProject> Start-AzureEmulator

会看到类似于下面的输出：

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

通过打开 Web 浏览器并浏览到输出中所示的本地地址（上面的示例输出中的 `http://127.0.0.1:81`），可以查看正在模拟器上运行的应用程序。

若要停止模拟器，请执行此命令：

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>发布应用程序

若要发布应用程序，需要先使用 [Import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) cmdlet 导入发布设置。 然后使用 [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) cmdlet 发布应用程序。 有关登录的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [PHP 开发人员中心](https://azure.microsoft.com/develop/php/)。

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[服务定义 (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[服务配置 (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 安装程序]: https://go.microsoft.com/fwlink/?LinkID=239648
