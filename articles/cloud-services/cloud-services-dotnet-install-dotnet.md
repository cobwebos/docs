---
title: "在 Azure 云服务角色上安装 .NET | Microsoft Docs"
description: "本文介绍如何在云服务 Web 角色和辅助角色上手动安装 .NET Framework"
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: a9cffa275ae6b9315b821d3160b17a997a1523f7
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---

# <a name="install-net-on-azure-cloud-services-roles"></a>在 Azure 云服务角色上安装 .NET
本文介绍如何安装不随 Azure 来宾 OS 一起提供的 .NET Framework 版本。 可使用来宾 OS 上的 .NET 配置云服务 web 角色和辅助角色。

例如，可在来宾 OS 系列 4 上安装 .NET 4.6.1（它不随 .NET 4.6 的任何版本一起提供）。 （来宾 OS 系列 5 随 .NET 4.6 一起提供。）有关最新的 Azure 来宾 OS 版本信息，请参阅 [Azure 来宾 OS 发行动态](cloud-services-guestos-update-matrix.md)。 

>[!IMPORTANT]
>Azure SDK 2.9 包含一个限制，限制将 .NET 4.6 部署到来宾 OS 系列 4 或更早版本。 有关此限制的修复方法可在 [Microsoft 文档](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)站点上找到。

要在 web 角色和辅助角色上安装 .NET，可将 .NET web 安装程序包括为云服务项目的一部分。 将安装程序作为角色启动任务的一部分启动。 

## <a name="add-the-net-installer-to-your-project"></a>将 .NET 安装程序添加到项目
若要下载 .NET Framework 的 Web 安装程序，请选择想要安装的版本：

* [.NET 4.7 Web 安装程序](http://go.microsoft.com/fwlink/?LinkId=825298)
* [.NET 4.6.1 Web 安装程序](http://go.microsoft.com/fwlink/?LinkId=671729)

添加 web 角色的安装程序：
  1. 在“解决方案资源管理器”中云服务项目中的“角色”下，右键单击 web 角色，然后选择“添加” > “新文件夹”。 创建一个名为 **bin** 的文件夹。
  2. 右键单击 bin 文件夹，并选择“添加” > >“现有项”。 选择 .NET 安装程序，并将它添加到 bin 文件夹。
  
添加辅助角色的安装程序：
* 右键单击辅助角色，然后选择“添加” > “现有项”。 选择 .NET 安装程序，并将它添加到角色。 

当以此方式将文件添加到角色内容文件夹时，会自动将其添加到云服务包。 然后会将文件部署到虚拟机上的一致位置。 对云服务中的每个 Web 和辅助角色重复此过程，以便所有角色都有安装程序的副本。

> [!NOTE]
> 即使应用程序面向 .NET 4.6，也应该在云服务角色上安装 .NET 4.6.1。 来宾 OS 包括知识库[更新 3098779](https://support.microsoft.com/kb/3098779)和[更新 3097997](https://support.microsoft.com/kb/3097997)。 如果在知识库更新上安装 .NET 4.6，运行 .NET 应用程序时可能会出现问题。 若要避免这些问题，请安装 .NET 4.6.1，而不是版本 4.6。 有关详细信息，请参阅[知识库文章 3118750](https://support.microsoft.com/kb/3118750)。
> 
> 

![包含安装程序文件的角色内容][1]

## <a name="define-startup-tasks-for-your-roles"></a>为角色定义启动任务
在角色启动之前，可以使用启动任务执行操作。 安装 .NET Framework 作为启动任务的一部分，可确保在运行任何应用程序代码之前已安装好 Framework。 有关启动任务的详细信息，请参阅[在 Azure 中运行启动任务](cloud-services-startup-tasks.md)。 

1. 将以下内容添加到所有角色的“WebRole”或“WorkerRole”节点下的 ServiceDefinition.csdef 文件中：
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    上述配置使用管理员特权来运行控制台命令 `install.cmd`，以安装 .NET Framework。 该配置还创建了一个名为“NETFXInstall”的“LocalStorage”元素。 启动脚本将临时文件夹设置为使用此本地存储资源。 
    
    > [!IMPORTANT]
    > 为确保能够正确安装框架，请将此资源的大小设置为至少 1,024 MB。
    
    有关启动任务的详细信息，请参阅[常见的 Azure 云服务启动任务](cloud-services-startup-tasks-common.md)。

2. 创建名为“install.cmd”的文件并将以下安装脚本添加到该文件。

    脚本将通过查询注册表来检查指定的 .NET Framework 版本是否已安装在计算机上。 如果未安装该 .NET 版本，则将打开 .Net Web 安装程序。 为帮助排查任何问题，该脚本会将所有活动记录到文件 startuptasklog-(current date and time).txt（存储在“InstallLogs”本地存储中）。

    > [!IMPORTANT]
    > 使用 Windows 记事本等基本文本编辑器创建 install.cmd 文件。 如果使用 Visual Studio 创建文本文件并将扩展名更改为 .cmd，则文件可能仍包含 UTF-8 字节顺序标记。 运行该脚本的第一行时，此标记可能会导致错误。 为避免此错误，可将脚本第一行设为可被字节顺序处理过程跳过的 REM 语句。 
    > 
    >
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" *****
    set netfx="NDP47"

    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."

    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit

    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%

    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp

    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp

    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp

    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"

    :NDP47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"

    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%

    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed

    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%

    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%

    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%

    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%

    :exit
    EXIT /B 0
    ```
   
   > [!NOTE]
   > 此脚本演示如何安装 .NET 4.5.2 或版本 4.6 以保持连续性，即使已可在 Azure 来宾 OS 上使用 .NET 4.5.2。 如[知识库文章 3118750](https://support.microsoft.com/kb/3118750) 中所述，应直接安装 .NET 4.6.1，而不是版本 4.6。
   > 
   > 

3. 按本主题前面所述，通过使用“解决方案资源管理器”中的“添加” > “现有项”，将 install.cmd 文件添加到每个角色。 

    完成此步骤后，所有角色应该都有 .NET 安装程序文件和 install.cmd 文件。

   ![包含所有文件的角色内容][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>配置诊断以将启动日志传输到 Blob 存储
为了方便排查安装问题，可以配置 Azure 诊断，将启动脚本或 .NET 安装程序生成的任何日志文件传输到 Azure Blob 存储。 使用这种方法，可从 blob 存储直接下载日志文件，而无需通过远程桌面访问角色，即可查看日志。

若要配置诊断，请打开 diagnostics.wadcfgx 文件，并在“Directories”节点下添加以下内容： 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

此 XML 将诊断配置为将“NETFXInstall”资源中日志目录中的文件传输到“netfx-install” blob 容器中的诊断存储帐户上。

## <a name="deploy-your-cloud-service"></a>部署云服务
部署云服务时，启动任务会安装 .NET Framework（如果尚未安装）。 框架安装过程中，云服务角色处于“忙碌”状态。 如果框架安装需要重启，可能同时会重启服务角色。 

## <a name="additional-resources"></a>其他资源
* [安装 .NET Framework][Installing the .NET Framework]
* [确定安装的 .NET Framework 版本][How to: Determine Which .NET Framework Versions Are Installed]
* [.NET Framework 安装故障排除][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

