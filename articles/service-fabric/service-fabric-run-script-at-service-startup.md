---
title: Azure Service Fabric 服务启动时运行脚本 | Microsoft Docs
description: 了解如何为 Service Fabric 服务安装程序入口点配置策略并在服务启动时运行脚本。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: bd2bb0d05029237242b42225a2c846c78a7c6de9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>以本地用户或系统帐户身份运行服务启动脚本
在启动 Service Fabric 服务可执行文件之前，可能需要进行某种配置或设置工作。  例如，配置环境变量。 可以在服务的服务清单中指定要在服务可执行文件启动前运行的脚本。 通过为服务安装程序入口点配置 RunAs 策略，可以更改在其下运行安装程序可执行文件的帐户。  使用单独的安装程序入口点，可在短时间内运行高权限配置，因此服务主机可执行文件不需要长时间使用高权限运行。

安装程序入口点（[服务清单](service-fabric-application-and-service-manifests.md)中的 **SetupEntryPoint**）是特权入口点，默认情况下以与 Service Fabric 相同的凭据（通常是 *NetworkService* 帐户）先于任何其他入口点运行。 **EntryPoint** 指定的可执行文件通常是长时间运行的服务主机。 **EntryPoint** 可执行文件在 **SetupEntryPoint** 可执行文件成功退出后运行。 如果总是终止或崩溃，则将监视并重启所产生的进程（再次从 **SetupEntryPoint** 开始）。 

## <a name="configure-the-service-setup-entry-point"></a>配置服务安装程序入口点
下面是无状态服务的一个简单服务清单示例，该示例在服务 **SetupEntryPoint** 中指定了安装程序脚本 *MySetup.bat*。  **Arguments** 用于在脚本运行时将参数传递给脚本。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>配置服务安装程序入口点的策略
默认情况下，服务安装程序入口点可执行文件在与 Service Fabric 相同的凭据（通常为 *NetworkService* 帐户）下运行。  在应用程序清单中，可以将安全权限更改为在本地系统帐户或管理员帐户下运行启动脚本。

### <a name="configure-the-policy-by-using-a-local-system-account"></a>使用本地系统帐户配置策略
下面的应用程序清单示例演示如何将服务安装程序入口点配置为在用户管理员帐户 (SetupAdminUser) 下运行。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

首先，以用户名（例如 SetupAdminUser）创建 **Principals** 节。 SetupAdminUser 用户帐户是 Administrators 系统组的成员。

接下来，在 **ServiceManifestImport** 节下面配置策略，以将此主体应用到 **SetupEntryPoint**。 此策略会告知 Service Fabric，当 **MySetup.bat** 文件运行时，它应该以 SetupAdminUser （具有管理员特权）身份运行。 由于*尚未*将策略应用到主入口点，因此 **MyServiceHost.exe** 中的代码将以系统 **NetworkService** 帐户运行。 这是用于运行所有服务入口点的默认帐户。

### <a name="configure-the-policy-by-using-local-system-accounts"></a>使用本地系统帐户配置策略
通常建议使用本地系统帐户，而不是管理员帐户运行启动脚本。 以 Administrators 组成员的身份运行 RunAs 策略通常效果不佳，因为计算机在默认情况下已启用用户访问控制 (UAC)。 在这种情况下，建议将 SetupEntryPoint 以 LocalSystem 身份运行，而不是以添加到 Administrators 组的本地用户身份来运行。 以下示例演示如何将 SetupEntryPoint 设置为作为 LocalSystem 运行：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> 对于 Linux 群集，若要以根身份运行服务或安装程序入口点，可以将 AccountType 指定为 LocalSystem。

## <a name="run-a-script-from-the-setup-entry-point"></a>从安装程序入口点运行脚本
现在将启动脚本添加到项目，以便在管理员特权下运行。 

在 Visual Studio 中，右键单击服务项目，并添加名为 *MySetup.bat* 的新文件。

接下来，请确保 *MySetup.bat* 文件已包含在服务包中。 默认情况下未包含该文件。 选择该文件，单击右键打开上下文菜单，并选择“**属性**”。 在“属性”对话框中，确保已将“**复制到输出目录**”设置为“**有更新时才复制**”。 请参阅下面的屏幕截图。

![SetupEntryPoint 批处理文件的 Visual Studio CopyToOutput][image1]

现在编辑 *MySetup.bat* 文件并添加以下命令，以设置系统环境变量和输出文本文件：

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

接下来，构建解决方案并将其部署到本地开发群集。 启动该服务之后（如 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 中所示），可以看到 MySetup.bat 文件在两个方面都已成功。 打开 PowerShell 命令提示符并键入：

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

接下来，记下已在 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 中部署并启动服务的节点名称， 例如“节点 2”。 接下来，导航到应用程序实例工作文件夹，找到显示 **TestVariable** 值的 out.txt 文件。 例如，如果此服务已部署到节点 2，则可以转到 **MyApplicationType** 的此路径：

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>从安装程序入口点运行 PowerShell 命令
若要从 **SetupEntryPoint** 点运行 PowerShell，可以在指向 PowerShell 文件的批处理文件中运行 **PowerShell.exe**。 首先，将 PowerShell 文件添加到服务项目（例如 **MySetup.ps1**）。 请记住设置“*如果较新则复制*”属性，以便文件还包括在服务包中。 下面的示例演示一个示例批处理文件，可启动名为 MySetup.ps1 的 PowerShell 文件，该文件用于设置系统环境变量 **TestVariable**。

MySetup.bat 可启动 PowerShell 文件：

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

在 PowerShell 文件中，添加以下内容来设置系统环境变量：

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> 默认情况下，当批处理文件运行时，它查找名为 **work** 应用程序文件夹中的文件。 在此示例中，当 MySetup.bat 运行时，我们希望它在同一文件夹（即应用程序**代码包**文件夹）中找到 MySetup.ps1 文件。 若要更改此文件夹，请按如下所示设置工作文件夹：
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>使用控制台重定向本地调试启动脚本
有时，需要查看运行安装程序脚本的控制台输出以便进行调试。 可以在服务清单中的安装程序入口点上设置控制台重定向策略，以便将输出写入文件。 文件输出将写入到部署和运行应用程序的群集节点上名为 **log** 的应用程序文件夹中。 

> [!WARNING]
> 永远不要在生产中部署的应用程序中使用控制台重定向策略，因为这可能会影响应用程序故障转移。 *仅*将其用于本地开发和调试目的。  
> 
> 

下面的服务清单示例演示如何使用 FileRetentionCount 值设置控制台重定向：

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

如果现在更改 MySetup.ps1 文件以写入 **Echo** 命令，该命令会写入到输出文件以进行调试：

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> 调试脚本后，请立即删除此控制台重定向策略。



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
* [了解应用程序和服务安全性](service-fabric-application-and-service-security.md)
* [了解应用程序模型](service-fabric-application-model.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
