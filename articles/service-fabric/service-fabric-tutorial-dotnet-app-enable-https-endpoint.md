---
title: 将 HTTPS 终结点添加到 Azure 中的 Service Fabric 应用 |Microsoft Docs
description: 本教程介绍如何向 ASP.NET Core 前端 Web 服务添加 HTTPS 终结点，以及如何将应用程序部署到群集。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/12/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 309a43d3383658029f4fe7f90f869888bac67bb1
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130044"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service"></a>教程：向 ASP.NET Core Web API 前端服务添加 HTTPS 终结点

本教程是一个系列中的第三部分。  你将了解如何在 ASP.NET Core 服务（在 Service Fabric 上运行）中启用 HTTPS。 完成后，你会有一个通过已启用 HTTPS 的 ASP.NET Core Web 前端在端口 443 上进行侦听的投票应用程序。 如果不希望根据[生成 .NET Service Fabric 应用程序](service-fabric-tutorial-deploy-app-to-party-cluster.md)中的说明手动创建投票应用程序，可以[下载源代码](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)（适用于已完成的应用程序）。

在该系列的第三部分中，你会学习如何：

> [!div class="checklist"]
> * 在服务中定义一个 HTTPS 终结点
> * 将 Kestrel 配置为使用 HTTPS
> * 在远程群集节点上安装 SSL 证书
> * 允许 NETWORK SERVICE 访问证书的私钥
> * 在 Azure 负载均衡器中打开端口 443
> * 将应用程序部署到远程群集

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [构建 .NET Service Fabric 应用程序](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [将应用程序部署到远程群集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * 向 ASP.NET Core 前端服务添加 HTTPS 终结点
> * [使用 Visual Studio Team Services 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [安装 Visual Studio 2017](https://www.visualstudio.com/) 版本 15.5 或更高版本，其中包含 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
* [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>获取证书或创建自签名开发证书

对于生产应用程序，请使用[证书颁发机构 (CA)](https://wikipedia.org/wiki/Certificate_authority) 提供的证书。 出于开发和测试目的，可以创建并使用自签名证书。 Service Fabric SDK 提供的 *CertSetup.ps1* 脚本可创建自签名证书并将其导入 `Cert:\LocalMachine\My` 证书存储。 以管理员身份打开命令提示符并运行以下命令即可创建使用者为“CN=localhost”的证书：

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=localhost
```

如果已经有证书 PFX 文件，请运行以下命令，将证书导入 `Cert:\LocalMachine\My` 证书存储：

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>在服务清单中定义一个 HTTPS 终结点

以**管理员**身份启动 Visual Studio，然后打开 Voting 解决方案。 在解决方案资源管理器中，打开 *VotingWeb/PackageRoot/ServiceManifest.xml*。 服务清单定义服务终结点。  找到 **Endpoints** 节，编辑现有的“ServiceEndpoint”终结点。  将名称更改为“EndpointHttps”，将协议设置为 *https*，类型设置为 *Input*，端口设置为 *443*。  保存所做更改。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>将 Kestrel 配置为使用 HTTPS

在“解决方案资源管理器”中，打开 *VotingWeb/VotingWeb.cs* 文件。  将 Kestrel 配置为使用 HTTPS，并在 `Cert:\LocalMachine\My` 存储中查找证书。 添加以下 using 语句：

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

更新 `ServiceInstanceListener`，以便使用新的 *EndpointHttps* 终结点并在端口 443 上进行侦听。

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

另请添加以下方法，这样 Kestrel 就能通过使用者在 `Cert:\LocalMachine\My` 存储中找到证书。  如果已使用以前的 PowerShell 命令创建自签名证书，请将“&lt;your_CN_value&gt;”替换为“localhost”，或者使用证书的 CN。

```csharp
private X509Certificate2 GetCertificateFromStore()
{
    var store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var currentCerts = certCollection.Find(X509FindType.FindBySubjectDistinguishedName, "CN=<your_CN_value>", false);
        return currentCerts.Count == 0 ? null : currentCerts[0];
    }
    finally
    {
        store.Close();
    }
}
```

## <a name="give-network-service-access-to-the-certificates-private-key"></a>允许 NETWORK SERVICE 访问证书的私钥

在前面的步骤中，已在开发计算机上将证书导入 `Cert:\LocalMachine\My` 存储。  另外，还必须显式允许运行服务（默认为 NETWORK SERVICE）的帐户访问证书的私钥。 可以手动执行此操作（使用 certlm.msc 工具），但最好是在服务清单的 **SetupEntryPoint** 中[配置启动脚本](service-fabric-run-script-at-service-startup.md)，以便自动运行 PowerShell 脚本。

### <a name="configure-the-service-setup-entry-point"></a>配置服务安装程序入口点

在解决方案资源管理器中，打开 *VotingWeb/PackageRoot/ServiceManifest.xml*。  在 **CodePackage** 节中添加 **SetupEntryPoint** 节点，然后添加 **ExeHost** 节点。  在 **ExeHost** 中将 **Program** 设置为“Setup.bat”，将 **WorkingFolder** 设置为“CodePackage”。  当 VotingWeb 服务启动时，先是 Setup.bat 脚本在 CodePackage 文件夹中执行，然后 VotingWeb.exe 才会启动。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>添加批处理和 PowerShell 设置脚本

若要从 **SetupEntryPoint** 点运行 PowerShell，可以在指向 PowerShell 文件的批处理文件中运行 PowerShell.exe。 首先，添加服务项目的批处理文件。  在“解决方案资源管理器”中，右键单击“VotingWeb”，选择“添加”->“新建项”，然后添加名为“Setup.bat”的新文件。  编辑 *Setup.bat* 文件，添加以下命令：

```bat
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

修改 *Setup.bat* 文件属性，将“复制到输出目录”设置为“如果较新则复制”。
![设置文件属性][image1]

在“解决方案资源管理器”中，右键单击“VotingWeb”，选择“添加”->“新建项”，然后添加名为“SetCertAccess.ps1”的新文件。  编辑 *SetCertAccess.ps1* 文件，添加以下脚本：

```powershell
$subject="localhost"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroupCertificate already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

Modify the *SetCertAccess.ps1* file properties to set **Copy to Output Directory** to "Copy if newer".
```

### <a name="run-the-setup-script-as-a-local-administrator"></a>以管理员身份运行设置脚本

默认情况下，服务设置入口点可执行文件运行时使用的凭据与 Service Fabric （通常为 NetworkService 帐户）使用的相同。 *SetCertAccess.ps1* 需要管理员特权。 在应用程序清单中，可以将安全权限更改为在本地管理员帐户下运行启动脚本。

在“解决方案资源管理器”中，打开 *Voting/ApplicationPackageRoot/ApplicationManifest.xml*。 首先创建 **Principals** 节，然后添加新用户（例如，“SetupAdminUser”）。 向 Administrators 系统组添加 SetupAdminUser 用户帐户。
接下来，在 VotingWebPkg **ServiceManifestImport** 节中配置 **RunAsPolicy**，以便向设置入口点应用 SetupAdminUser 主体。 此策略告知 Service Fabric，Setup.bat 文件以 SetupAdminUser 身份（具有管理员特权）运行。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
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

## <a name="run-the-application-locally"></a>在本地运行应用程序

在“解决方案资源管理器”中，选择 **Voting** 应用程序并将“应用程序 URL”属性设置为“https://localhost:443”。

保存所有文件并按 F5，以便在本地运行应用程序。  在应用程序部署完以后，Web 浏览器会打开到 [https://localhost:443](https://localhost:443)。 如果使用自签名证书，则会看到一个警告，指出电脑不信任此网站的安全性。  转到该网页。

![Voting 应用程序][image2]

## <a name="install-certificate-on-cluster-nodes"></a>在群集节点上安装证书

在将应用程序部署到 Azure 之前，请将证书安装到远程群集节点的 `Cert:\LocalMachine\My` 存储中。  当前端 Web 服务在群集节点上启动时，启动脚本会查找证书并配置访问权限。

首先，将证书导出到 PFX 文件。 打开 certlm.msc 应用程序，导航到“个人”>“证书”。  右键单击 *localhost* 证书，选择“所有任务”>“导出”。

![导出证书][image4]

在导出向导中，选择“是，导出私钥”，然后选择“个人信息交换(PFX)”格式。  将文件导出到 *C:\Users\sfuser\votingappcert.pfx*。

接下来，使用 [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) cmdlet 在远程群集上安装证书。

> [!Warning]
> 对于开发和测试应用程序，自签名证书已足够。 对于生产应用程序，请使用[证书颁发机构 (CA)](https://wikipedia.org/wiki/Certificate_authority) 提供的证书，而不是自签名证书。

```powershell
Connect-AzureRmAccount

$vaultname="sftestvault"
$certname="VotingAppPFX"
$certpw="!Password321#"
$groupname="voting_RG"
$clustername = "votinghttps"
$ExistingPfxFilePath="C:\Users\sfuser\votingappcert.pfx"

$appcertpwd = ConvertTo-SecureString -String $certpw -AsPlainText -Force

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $certpw

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $certpw
   } | ConvertTo-Json

$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
Write-Host "Writing secret to $certname in vault $vaultname"
$secret = Set-AzureKeyVaultSecret -VaultName $vaultname -Name $certname -SecretValue $secretValue

# Add a certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $secret.Id -Verbose
```

## <a name="open-port-443-in-the-azure-load-balancer"></a>在 Azure 负载均衡器中打开端口 443

在负载均衡器中打开端口 443（如果尚未打开）。

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzureRmResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzureRmLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzureRmLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzureRmLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzureRmLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzureRmLoadBalancer
```

## <a name="deploy-the-application-to-azure"></a>将应用程序部署到 Azure

保存所有文件，从“调试”切换到“发布”，然后按 F6 进行重新生成。  在“解决方案资源管理器”中，右键单击“Voting”并选择“发布”。 选择在[将应用程序部署到群集](service-fabric-tutorial-deploy-app-to-party-cluster.md)中创建的群集的连接终结点，或者选择另一群集。  单击“发布”，将应用程序发布到远程群集。

当应用程序部署后，打开 Web 浏览器，导航到 [https://mycluster.region.cloudapp.azure.com:443](https://mycluster.region.cloudapp.azure.com:443)（使用群集的连接终结点更新 URL）。 如果使用自签名证书，则会看到一个警告，指出电脑不信任此网站的安全性。  转到该网页。

![Voting 应用程序][image3]

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 在服务中定义一个 HTTPS 终结点
> * 将 Kestrel 配置为使用 HTTPS
> * 在远程群集节点上安装 SSL 证书
> * 允许 NETWORK SERVICE 访问证书的私钥
> * 在 Azure 负载均衡器中打开端口 443
> * 将应用程序部署到远程群集

转到下一教程：
> [!div class="nextstepaction"]
> [使用 Visual Studio Team Services 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
