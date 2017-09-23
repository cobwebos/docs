---
title: "了解 Azure 微服务安全策略 | Microsoft Docs"
description: "有关如何使用系统帐户和本地安全帐户运行 Service Fabric 应用程序概述，包括应用程序在启动之前，需要在其中执行某些特权操作的 SetupEntry 点"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: e673b45a43a06d18040c3437caf8765704d5c36a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="configure-security-policies-for-your-application"></a>配置应用程序的安全策略
使用 Azure Service Fabric，可以保护群集中以不同用户帐户运行的应用程序。 Service Fabric 还有助于在部署时使用用户帐户来保护应用程序所用的资源，例如文件、目录和证书。 这样，即使在共享托管环境中，也可确保运行的应用程序彼此更安全。

默认情况下，Service Fabric 应用程序在运行 Fabric.exe 程序的帐户之下运行。 Service Fabric 还可让你使用应用程序清单中指定的本地用户帐户或本地系统帐户运行应用程序。 受支持的本地系统帐户类型为 **LocalUser**、**NetworkService**、**LocalService** 和 **LocalSystem**。

 使用独立安装程序在数据中心中的 Windows Server 上运行 Service Fabric 时，可以使用 Active Directory 域帐户，包括组托管服务帐户。

可以定义和创建用户组，以便将一个或多个要统一管理的用户添加到每个组。 如果不同的服务入口点有多个用户，而且这些用户需要拥有可在组级别使用的某些常用权限，则这种做法特别有用。

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>配置服务安装程序入口点的策略
如[应用程序模型](service-fabric-application-model.md)中所述，安装程序入口点 **SetupEntryPoint** 是特权入口点，以与 Service Fabric 相同的凭据（通常是 *NetworkService* 帐户）先于任何其他入口点运行。 **EntryPoint** 指定的可执行文件通常是长时间运行的服务主机。 因此，具有单独的安装程序入口点可避免长时间使用高特权运行服务主机可执行文件。 由 **EntryPoint** 指定的可执行文件在 **SetupEntryPoint** 成功退出后运行。 如果总是终止或崩溃，则将监视并重启所产生的进程（再次从 **SetupEntryPoint** 开始）。

下面是一个简单的服务清单示例，其中显示服务的 SetupEntryPoint 和主要 EntryPoint。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>使用本地帐户配置策略
在配置服务以获取设置入口点之后，你可以在应用程序清单中更改用于运行服务的安全权限。 以下示例演示如何将服务配置为以用户管理员帐户特权运行。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
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

首先，以用户名（例如 SetupAdminUser）创建 **Principals** 节。 这指明用户是管理员系统组的成员。

接下来，在 **ServiceManifestImport** 节下面配置策略，以将此主体应用到 **SetupEntryPoint**。 这会告知 Service Fabric，当 **MySetup.bat** 文件运行时，它应该是具有管理员特权的 `RunAs`。 假设你*尚未*将策略应用到主入口点，则 **MyServiceHost.exe** 中的代码将以系统 **NetworkService** 帐户运行。 这是用于运行所有服务入口点的默认帐户。

现在让我们将 MySetup.bat 文件添加 Visual Studio 项目，以测试管理员特权。 在 Visual Studio 中，右键单击服务项目，然后添加名为 MySetup.bat 的新文件。

接下来，确保 MySetup.bat 文件已包含在服务包中。 默认情况下未包含该文件。 选择该文件，单击右键打开上下文菜单，然后选择“**属性**”。 在“属性”对话框中，确保已将“**复制到输出目录**”设置为“**有更新时才复制**”。 请参阅下面的屏幕截图。

![SetupEntryPoint 批处理文件的 Visual Studio CopyToOutput][image1]

现在打开 MySetup.bat 文件并添加以下命令：

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

接下来，构建解决方案并将其部署到本地开发群集。 启动该服务之后（如 Service Fabric Explorer 中所示），你可以看到 MySetup.bat 文件在两个方面都已成功。 打开 PowerShell 命令提示符并键入：

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

接下来，记下已在 Service Fabric Explorer 中部署并启动服务的节点名称，例如“节点 2”。 接下来，导航到应用程序实例工作文件夹，找到显示 **TestVariable** 值的 out.txt 文件。 例如，如果此服务已部署到节点 2，则你可以转到 **MyApplicationType** 的此路径：

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>使用本地系统帐户配置策略
通常建议使用本地系统帐户，而不是管理员帐户运行启动脚本。 用管理员组成员的身份运行 RunAs 策略通常效果不佳，因为计算机在默认情况下已启用用户访问控制 (UAC)。 在这种情况下，**建议将 SetupEntryPoint 作为 LocalSystem 运行，而不要作为添加到管理员组的本地用户来运行**。 以下示例演示如何将 SetupEntryPoint 设置为作为 LocalSystem 运行：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>从 SetupEntryPoint 启动 PowerShell 命令
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

## <a name="use-console-redirection-for-local-debugging"></a>对本地调试使用控制台重定向
有时，需要查看运行脚本的控制台输出以便进行调试。 为此，可以设置控制台重定向策略以将输出写入到文件。 文件输出写入到部署和运行应用程序的节点上的应用程序文件夹 **log** 中。 （上面的示例说明了在何处找到此文件夹）。

> [!WARNING]
> 永远不要在生产中部署的应用程序中使用控制台重定向策略，因为这可能会影响应用程序故障转移。 *仅*将其用于本地开发和调试目的。  
> 
> 

下面的示例演示如何使用 FileRetentionCount 值设置控制台重定向：

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

**调试脚本后，请立即删除此控制台重定向策略**。

## <a name="configure-a-policy-for-service-code-packages"></a>配置服务代码包的策略
在前面步骤中，你已了解如何将 RunAs 策略应用于 SetupEntryPoint。 让我们深入了解如何创建可作为服务策略应用的不同主体。

### <a name="create-local-user-groups"></a>创建本地用户组
可以定义和创建允许将一个或多个用户添加到组的用户组。 如果不同的服务入口点对应有多个用户，而且这些用户需要拥有特定的常见组级别权限，这种做法就特别有用。 下面的示例演示一个名为 **LocalAdminGroup** 的具有管理员权限的本地组。 Customer1 和 Customer2 这两个用户已成为此本地组的成员。

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

### <a name="create-local-users"></a>创建本地用户
你可以创建一个本地用户，用于帮助保护应用程序中的服务。 当在应用程序清单的主体部分中指定 **LocalUser** 帐户类型时，Service Fabric 在部署应用程序的计算机上创建本地用户帐户。 默认情况下，这些帐户的名称不与应用程序清单中指定的名称相同（例如，以下示例中的 Customer3）。 相反，它们是动态生成的并带有随机密码。

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

如果应用程序要求所有计算机上的用户帐户和密码都相同（例如，为启用 NTLM 身份验证），群集清单必须将 NTLMAuthenticationEnabled 设置为 true。 群集清单还必须指定用于对所有计算机生成相同密码的 NTLMAuthenticationPasswordSecret。

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>将策略分配到服务代码包
**ServiceManifestImport** 的 **RunAsPolicy** 部分指定 principals 部分中应该用来运行代码包的帐户。 它还将服务清单中的代码包关联到 principals 节中的用户帐户。 可以为设置入口点或主入口点指定此策略，或者指定 `All` 以将其应用到两者。 以下示例演示了应用的不同策略：

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

如果未指定 **EntryPointType**，则 EntryPointType 的默认值设置为“Main”。 如果你想要以系统帐户运行某些高特权设置操作，则指定 **SetupEntryPoint** 将特别有用。 实际服务代码可使用较低特权的帐户来运行。

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>将默认策略应用到所有服务代码包
**DefaultRunAsPolicy** 部分用于针对未定义特定 **RunAsPolicy** 的所有代码包指定默认用户帐户。 如果在应用程序所用的服务清单中指定的大多数代码包必须以同一用户运行，则应用程序可以只定义该用户帐户的默认 RunAs 策略。 以下示例指定如果代码包未指定 **RunAsPolicy**，则此代码包应该以 principals 部分指定的 **MyDefaultAccount** 运行。

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>使用 Active Directory 域组或用户
对于使用独立安装程序在 Windows Server 上安装的 Service Fabric 实例，可以使用 Active Directory 用户或组帐户的凭据来运行服务。 这是域中的本地 Active Directory，不是 Azure Active Directory (Azure AD)。 通过使用域用户或组，可以访问域中已被授予权限的其他资源（例如文件共享）。

下面的示例演示名为 *TestUser* 的 Active Directory 用户，其域密码使用名为 *MyCert* 的证书进行了加密。 可以使用 `Invoke-ServiceFabricEncryptText` PowerShell 命令创建密码文本。 请参阅[管理 Service Fabric 应用程序中的机密](service-fabric-application-secret-management.md)获取详细信息。

用于解密密码的证书私钥必须使用带外方法（在 Azure 中通过 Azure Resource Manager）部署到本地计算机。 这样，当 Service Fabric 将服务包部署到计算机时，便能够解密密码和用户名，向 Active Directory 进行身份验证以使用这些凭据运行。

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>使用组托管服务帐户。
对于使用独立安装程序在 Windows Server 上安装的 Service Fabric 实例，可以以组托管服务帐户 (gMSA) 身份来运行服务。 注意：这是域中的本地 Active Directory，不是 Azure Active Directory (Azure AD)。 使用 gMSA 时，没有密码或加密密码存储在 `Application Manifest` 中。

以下示例演示如何创建一个名为 *svc-Test$* 的 gMSA 帐户；如何将该托管服务帐户部署到群集节点；以及如何配置用户主体。

##### <a name="prerequisites"></a>先决条件。
- 域需要 KDS 根密钥。
- 域必须位于 Windows Server 2012 或更高功能级别上。

##### <a name="example"></a>示例
1. 让 active directory 域管理员使用 `New-ADServiceAccount` commandlet 创建组托管服务帐户，并确保 `PrincipalsAllowedToRetrieveManagedPassword` 包括所有 service fabric 群集节点。 请注意， `AccountName`、`DnsHostName` 和 `ServicePrincipalName` 必须是唯一的。
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. 在每个 Service Fabric 群集节点（例如，`SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`）上，安装并测试 gMSA。
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. 配置用户主体，并配置 RunAsPolicy 以引用用户。
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>为 HTTP 和 HTTPS 终结点分配安全访问策略
如果向服务应用 RunAs 策略务，而服务清单声明具有 HTTP 协议的终结点资源，则必须指定 **SecurityAccessPolicy**，以确保分配给这些终结点的端口都已针对用来运行服务的 RunAs 用户帐户正确列入访问控制列表中。 否则，**http.sys** 将无权访问服务，并且将无法从客户端调用。 以下示例将 Customer1 帐户应用于名为“EndpointName”的终结点，并向它授予完全访问权限。

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

针对 HTTPS 终结点，你还必须指出要返回给客户端的证书名称。 可以使用 **EndpointBindingPolicy** 结合应用程序清单的 certificates 部分定义的证书，来实现此目的。

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>使用 https 终结点升级多个应用程序
请注意，使用 https时，不要将相同端口用于同一应用程序的不同实例。 原因是，Service Fabric 无法升级应用程序实例之一的证书。 例如，如果应用程序 1 或应用程序 2 都要将证书 1 升级为证书 2。 升级时，Service Fabric 可能已使用 http.sys 清除了证书 1 注册，即使其他应用程序仍在使用它，也不例外。 为了防止发生这种情况，Service Fabric 检测到另一个应用程序实例已在带证书的端口上注册（由于 http.sys），导致操作失败。

因此，Service Fabric 不支持通过以下方法升级两个不同的服务：在不同的应用程序实例中使用同一端口。 也就是说，不能对同一端口上的不同服务使用同一证书。 如果需要在同一端口上使用共享证书，请务必将服务放置在具有放置约束的不同计算机上。 或者，如果可以，考虑将 Service Fabric 动态端口用于每个应用程序实例中的各个服务。 

如果 https 升级失败，则会看到内容为“Windows HTTP Server API 不支持对共享端口的应用程序使用多个证书”的错误警告。

## <a name="a-complete-application-manifest-example"></a>完整应用程序清单的示例
以下应用程序清单显示了许多不同的设置：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
* [了解应用程序模型](service-fabric-application-model.md)
* [在服务清单中指定资源](service-fabric-service-manifest-resources.md)
* [部署应用程序](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

