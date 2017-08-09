---
title: "在云服务角色上安装 .NET | Microsoft Docs"
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
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e6154d990e10f67d4b30b889a62a99cedcbfccbe
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="install-net-on-a-cloud-service-role"></a>在云服务角色上安装 .NET
本文介绍如何在云服务 Web 角色和辅助角色上安装与来宾 OS 随附的版本所不同的 .NET Framework。 例如，可以使用这些步骤在不带任何 .NET 4.6 版本的 Azure 来宾 OS 系列 4 上安装 .NET 4.6.1。 有关最新的来宾 OS 版本信息，请参阅 [Azure 来宾 OS 发行动态](cloud-services-guestos-update-matrix.md)。

>[!NOTE]
>来宾 OS 5 包含 .NET 4.6

>[!IMPORTANT]
>Azure SDK 2.9 包含一个限制，限制将 .NET 4.6 部署到来宾 OS 4 或更低版本。 [此处](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9)提供了一个解决方法。

在 Web 角色和辅助角色上安装 .NET 的过程涉及到在云项目中添加 .NET 安装包，并在执行角色的启动任务过程中启动安装程序。  

## <a name="add-the-net-installer-to-your-project"></a>将 .NET 安装程序添加到项目
* 下载安装.NET Framework 的 Web 安装程序
  * [.NET 4.7 Web 安装程序](http://go.microsoft.com/fwlink/?LinkId=825298)
  * [.NET 4.6.1 Web 安装程序](http://go.microsoft.com/fwlink/?LinkId=671729)

* 对于 Web 角色
  1. 在解决方案资源管理器中，云服务项目中的“角色”下，右键单击角色，然后选择“添加”>“新文件夹”。 创建一个名为 *bin* 的文件夹
  2. 右键单击 bin 文件夹，并选择“添加” > “现有项”。 选择 .NET 安装程序，并将它添加到 bin 文件夹。
  
* 对于辅助角色
  1. 右键单击角色，然后选择“添加”>“现有项”。 选择 .NET 安装程序，并将它添加到角色。 

以此方式添加到角色内容文件夹的文件会自动添加到云服务包，并部署到虚拟机上的相应位置。 对云服务中的所有 Web 和辅助角色重复此过程，使所有角色都有安装程序的副本。

> [!NOTE]
> 即使应用程序面向 .NET 4.6，也应该在云服务角色上安装 .NET 4.6.1。 Azure 来宾 OS 包含更新 [3098779](https://support.microsoft.com/kb/3098779) 和 [3097997](https://support.microsoft.com/kb/3097997)。 在这些更新的顶层安装 .NET 4.6 可能会在运行 .NET 应用程序时造成问题，因此，应该直接安装 .NET 4.6.1 而不是 .NET 4.6。 有关详细信息，请参阅 [KB 3118750](https://support.microsoft.com/kb/3118750)。
> 
> 

![包含安装程序文件的角色内容][1]

## <a name="define-startup-tasks-for-your-roles"></a>为角色定义启动任务
在角色启动之前，可以使用启动任务执行操作。 安装 .NET Framework 作为启动任务的一部分，可确保在运行任何应用程序代码之前已安装好 Framework。 有关启动任务的详细信息，请参阅 [Run Startup Tasks in Azure](cloud-services-startup-tasks.md)（在 Azure 中运行启动任务）。 

1. 将以下内容添加到所有角色的 **WebRole** 或 **WorkerRole** 节点下的 *ServiceDefinition.csdef* 文件中：
   
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
   
    上述配置使用管理员特权来运行控制台命令 install.cmd，以安装 .NET Framework。 该配置还创建了一个名为 NETFXInstall 的 LocalStorage。 启动脚本将临时文件夹设置为使用此本地存储资源。 请务必将此资源的大小设置为至少 1024MB，确保能够正确安装框架。 有关启动任务的详细信息，请参阅：[常见的云服务启动任务](cloud-services-startup-tasks-common.md) 

2. 创建文件 install.cmd，右键单击角色并选择“添加”>“现有项...”将此文件添加到所有角色。 因此，所有角色现在应该都有 .NET 安装程序文件，以及 install.cmd 文件。
   
   ![包含所有文件的角色内容][2]
   
   > [!NOTE]
   > 使用记事本之类的基本文字编辑器来创建此文件。 如果使用 Visual Studio 来创建文本文件，并将其重命名为“.cmd”，则此文件可能仍包含 UTF-8 字节顺序标记，且运行第一行脚本时会出现错误。 请确保该文件的第一行是 REM 命令，这可能会跳过 UTF-8 字节顺序标记处理。 
   > 
   > 

3. 将以下脚本添加到 **install.cmd** 文件：
   
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
   
    安装脚本将通过查询注册表来检查指定的 .NET Framework 版本是否已安装在计算机上。 如果未安装该 .NET 版本，.Net Web 安装程序会启动。 为帮助排查任何问题，该脚本会将所有活动记录到名为 *startuptasklog-(currentdatetime).txt* 的文件（存储在 *InstallLogs* 本地存储中）。
   
   > [!NOTE]
   > 为了保持内容连贯，该脚本仍会演示如何安装 .NET 4.5.2 或 .NET 4.6。 不需要手动安装 .NET 4.5.2，因为 Azure 来宾 OS 上已提供该组件。 由于 [KB 3118750](https://support.microsoft.com/kb/3118750) 中所述的原因，用户应该直接安装 .NET 4.6.1，而不要安装 .NET 4.6。
   > 
   > 

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>配置诊断以将启动任务日志传输到 Blob 存储
为了方便排查任何安装问题，可以配置 Azure 诊断，将启动脚本或 .NET 安装程序生成的任何日志文件传输到 Blob 存储。 使用这种方法只需从 blob 存储直接下载日志文件，而无需通过远程桌面访问角色，即可查看日志。

若要配置诊断，请打开 diagnostics.wadcfgx，并在 Directories 节点下添加以下内容： 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

此 xml 将 azure 诊断配置为将 NETFXInstall 资源下日志目录中的所有文件传输到 netfx-install blob 容器中的诊断存储帐户上。

## <a name="deploying-your-service"></a>部署服务
部署服务时，启动任务会安装 .NET Framework（如果尚未安装）。 安装 Framework 时，角色处于忙碌状态，而且如果 Framework 安装需要，甚至可能会重启角色。 

## <a name="additional-resources"></a>其他资源
* [安装 .NET Framework][Installing the .NET Framework]
* [如何：确定安装的 .NET Framework 版本][How to: Determine Which .NET Framework Versions Are Installed]
* [.NET Framework 安装故障排除][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



