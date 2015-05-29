<properties
   pageTitle="在云服务角色上安装 .NET"
   description="本文介绍如何在云服务 Web 角色和辅助角色上手动安装 .NET Framework"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/23/2015"
   ms.author="saurabh"/>

# 在云服务角色上安装 .NET 

本文介绍如何在云服务 Web 角色和辅助角色上安装 .NET Framework。可以使用以下步骤来安装默认情况下不存在于 Azure 来宾操作系统上的 .NET Framework 版本。有关最新的来宾操作系统版本信息，请参阅 [Azure 来宾操作系统版本和 SDK 兼容性表格](cloud-services-guestos-update-matrix.md)。

在 Web 角色和辅助角色上安装 .NET 的过程涉及到在云项目中添加 .NET 安装程序包，并在执行角色的启动任务过程中启动安装程序。  

## 将 .NET 安装程序添加到项目
1. 下载 [.NET 4.5.2 Web 安装程序](http://www.microsoft.com/zh-cn/download/details.aspx?id=42643)
2. 对于 Web 角色
  1. 在"解决方案资源管理器"中，于云服务项目中的"角色"下，右键单击你的角色，然后选择"添加 > 新文件夹"。创建名为 *bin* 的文件夹
  2. 右键单击 **bin** 文件夹，然后选择"添加 > 现有项"。选择 .NET 安装程序，并将它添加到 bin 文件夹。
3. 对于辅助角色
  1. 右键单击你的角色，然后选择"添加 > 现有项"。选择 .NET 安装程序，并将它添加到角色。 

以此方式添加到角色内容文件夹的文件会自动添加到云服务包，并部署到虚拟机上的一致位置。对云服务中的所有 Web 和辅助角色重复此过程，使所有角色都有安装程序的副本。

![Role Contents with installer files][1]

## 为角色定义启动任务
启动任务可让你在启动角色之前执行操作。将 .NET Framework 作为启动任务的一部分安装，可确保在运行任何应用程序代码之前已安装好 Framework。有关有关启动任务的详细信息，请参阅：[在 Azure 中运行启动任务](https://msdn.microsoft.com/library/azure/hh180155.aspx)。 

1. 将以下内容添加所有角色的 *WebRole* 或 *WorkerRole* 节点下的 *ServiceDefinition.csdef* 文件：
	
	```xml
	 <LocalResources>
	    <LocalStorage name="InstallLogs" sizeInMB="5" cleanOnRoleRecycle="false" />
	 </LocalResources>
	 <Startup>
	    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
	        <Environment>
	        <Variable name="PathToInstallLogs">
	        <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='InstallLogs']/@path" />
	        </Variable>
	        </Environment>
	    </Task>
	 </Startup>
	```

	上述配置将使用管理员特权来执行控制台命令 *install.cmd*，以安装 .NET Framework。此配置还会创建名为 *InstallLogs* 的 LocalStorage，以存储安装脚本所创建的任何日志信息。有关详细信息，请参阅：[在启动时使用本地存储来存储文件](https://msdn.microsoft.com/library/azure/hh974419.aspx) 

2. 创建包含以下内容的 install.cmd 文件：

	```
	REM install.cmd to install .NET Framework
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set startuptasklog=%PathToInstallLogs%\startuptasklog-%timestamp%.txt
	set netfxinstallerlog = %PathToInstallLogs%\NetFXInstallerLog-%timestamp%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo Checking if .NET 4.5.2 is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find "0x5cbf5"
	if %ERRORLEVEL%== 0 goto end
	echo Installing .NET 4.5.2. Log: %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0NDP452-KB2901954-Web.exe /q /serialdownload /log %netfxinstallerlog%
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	```

	安装脚本会通过查询注册表来检查 .NET 4.5.2 是否已在计算机上安装。如果未安装.NET 4.5.2，.Net Web 安装程序将会启动。为帮助排查任何问题，该脚本会将所有活动记录到名为 *startuptasklog-(currentdatetime).txt* 的文件（存储在 *InstallLogs* 本地存储中）。

	> [AZURE.NOTE] 使用记事本之类的简单文字编辑器来创建这个文件。如果使用 Visual Studio 来创建文本文件，然后将它重命名为".cmd"，则此文件可能仍包含 UTF-8 字节顺序标记，并在运行第一行脚本时出现错误。如果你要使用 Visual Studio 来创建文件，请在文件的第一行保留添加 REM（备注），以便在运行时将它忽略。 
      
3. 右键单击角色并选择"添加 > 现有项..."即可将 install.cmd 文件添加到所有角色。因此，所有角色现在应该都有 .NET 安装程序文件，以及 install.cmd 文件。
	
	![Role Contents with all files][2] 

## 配置诊断以将启动任务日志传输到 Blob 存储（可选）
你可以选择性地配置 Azure 诊断，将启动脚本或 .NET 安装程序生成的任何日志文件传输到 Blob 存储。使用这种方法可以从 Blob 存储直接下载日志文件，而无需通过远程桌面访问角色，即可查看日志。

若要配置诊断，请打开 *diagnostics.wadcfgx*，并在 *<Directories>* 节点下添加以下内容： 

```xml 
<DataSources>
    <DirectoryConfiguration containerName="netfx-install">
    <LocalResource name="InstallLogs" relativePath="."/>
    </DirectoryConfiguration>
</DataSources>
```

这会将 Azure 诊断配置为将 *InstallLogs* 资源中的所有文件传输到 *netfx-install* Blob 容器中的诊断存储帐户。

## 部署服务 
部署服务时，启动任务将会运行并安装 .NET Framework（如果尚未安装）。安装 Framework 时，角色将处于忙碌状态，而且甚到可能会重新启动（如果 Framework 安装有此要求）。 


## 其他资源

- [安装 .NET Framework][]
- [如何：确定安装的 .NET Framework 版本][]
- [.NET Framework 安装疑难解答][]

[如何：确定安装的 .NET Framework 版本]: https://msdn.microsoft.com/library/hh925568.aspx
[安装 .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[.NET Framework 安装疑难解答]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

<!---HONumber=56-->