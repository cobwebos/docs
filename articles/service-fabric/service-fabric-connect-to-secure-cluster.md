---
title: 安全连接 Azure Service Fabric 群集 | Microsoft Docs
description: 介绍如何对访问 Service Fabric 群集的客户端进行身份验证，以及如何保护客户端与群集之间的通信。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2018
ms.author: ryanwi
ms.openlocfilehash: 2ddb72f267fc46d7980007d41c5d512f50eaf47e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="connect-to-a-secure-cluster"></a>连接到安全群集

当客户端连接到 Service Fabric 群集节点时，可以使用证书安全性或 Azure Active Directory (AAD) 与客户端建立经过身份验证的安全通信。 此身份验证可确保只有经过授权的用户才能访问该群集和部署的应用程序，以及执行管理任务。  创建群集时，必须事先在该群集上启用证书或 AAD 安全性。  有关群集安全方案的详细信息，请参阅[群集安全性](service-fabric-cluster-security.md)。 要连接到使用证书进行保护的群集，请在将要连接到群集的计算机上[设置客户端证书](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert)。 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>使用 Azure Service Fabric CLI (sfctl) 连接到安全群集

可以通过多种不同方式使用 Service Fabric CLI (sfctl) 连接到安全群集。 使用客户端证书进行身份验证时，证书详细信息必须与部署到群集节点的证书匹配。 如果证书具有证书颁发机构 (CA)，则需要另外指定受信任的 CA。

可以使用 `sfctl cluster select` 命令连接到群集。

可以通过两种不同方式指定客户端证书：作为证书和密钥对，或作为单个 pem 文件。 对于受密码保护的 `pem` 文件，系统将自动提示你输入密码。

若要将客户端证书指定为 pem 文件，请在 `--pem` 参数中指定文件路径。 例如：

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

在运行任何命令之前，受密码保护的 pem 文件将提示用户输入密码。

若要指定证书，密钥对将使用 `--cert` 和 `--key` 参数来指定每个相应文件的文件路径。

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

有时用于保护测试或开发群集的证书未通过证书验证。 若要绕过证书验证，请指定 `--no-verify` 选项。 例如：

> [!WARNING]
> 连接到生产 Service Fabric 群集时，不要使用 `no-verify` 选项。

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

此外，可以指定受信任 CA 证书或单个证书的目录的路径。 若要指定这些路径，请使用 `--ca` 参数。 例如：

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

连接后，应能够[运行其他 sfctl 命令](service-fabric-cli.md)与群集进行交互。

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>使用 PowerShell 连接到群集
通过 PowerShell 对群集执行操作之前，请先建立与群集的连接。 群集连接用于给定 PowerShell 会话中的所有后续命令。

### <a name="connect-to-an-unsecure-cluster"></a>连接到不安全的群集

要连接到不安全的群集，请将群集终结点地址提供给 **Connect-ServiceFabricCluster** 命令：

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>使用 Azure Active Directory 连接到安全群集

若要连接到使用 Azure Active Directory 对群集管理员访问授权的安全群集，请提供群集证书指纹并使用 *AzureActiveDirectory* 标志。  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用客户端证书连接到安全群集
运行以下 PowerShell 命令，连接到使用客户端证书对管理员访问授权的安全群集。 

#### <a name="connect-using-certificate-common-name"></a>使用证书公用名称连接
提供群集证书公用名称，以及已被授予群集管理权限的客户端证书的公用名称。 证书详细信息必须与群集节点上的证书匹配。

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
ServerCommonName 是群集节点上安装的服务器证书的公用名称。 FindValue 是管理客户端证书的公用名称。 填充参数以后，命令将如以下示例所示：
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>使用 Windows Active Directory 连接到安全群集
如果使用 AD 安全性部署独立群集，请通过附加开关项“WindowsCredential”连接到群集。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>使用 FabricClient API 连接到群集
Service Fabric SDK 提供用于群集管理的 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) 类。 若要使用 FabricClient API，请获取 Microsoft.ServiceFabric NuGet 包。

### <a name="connect-to-an-unsecure-cluster"></a>连接到不安全的群集

若要连接到远程的不安全群集，请创建 FabricClient 实例并提供群集地址：

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

对于从群集内部运行的代码（例如在可靠服务中运行的代码），可在*不* 指定群集地址的情况下创建 FabricClient。 FabricClient 可连接到当前正在运行代码的节点上的本地管理网关，避免额外的网络跃点。

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用客户端证书连接到安全群集

群集中的节点必须具有有效的证书，在 SAN 中，这些证书的公用名或 DNS 名出现在 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) 上设置的 [RemoteCommonNames 属性](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames)中。 按此过程操作就可以在客户端与群集节点之间进行相互身份验证。

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>使用 Azure Active Directory 以交互方式连接到安全群集

以下示例针对客户端标识使用 Azure Active Directory，针对服务器标识使用服务器证书。

连接到群集后，会自动弹出一个用于交互式登录的对话框窗口。

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>使用 Azure Active Directory 以非交互方式连接到安全群集

以下示例依赖于 Microsoft.IdentityModel.Clients.ActiveDirectory，版本：2.19.208020213。

有关 AAD 令牌获取过程的详细信息，请参阅 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx)。

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>使用 Azure Active Directory 在不预先了解元数据的情况下连接到安全群集

以下示例使用非交互式令牌获取过程，但可以相同的方法构建自定义交互式令牌获取体验。 将从群集配置中读取令牌获取过程所需的 Azure Active Directory 元数据。

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 连接到安全群集
要访问给定群集的 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)，请将浏览器指向：

`http://<your-cluster-endpoint>:19080/Explorer`

Azure 门户的群集基本信息窗格中也提供了完整 URL。

若要使用浏览器连接到 Windows 或 OS X 上的安全群集，可以导入客户端证书，浏览器将提示你提供要用于连接群集的证书。  在 Linux 计算机上，需要使用高级浏览器设置（每个浏览器具有不同的机制）导入证书并将其指向磁盘上的证书位置。

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>使用 Azure Active Directory 连接到安全群集

要连接到使用 AAD 进行保护的群集，请将浏览器指向：

`https://<your-cluster-endpoint>:19080/Explorer`

系统会自动提示用户使用 AAD 登录。

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用客户端证书连接到安全群集

要连接到使用证书进行保护的群集，请将浏览器指向：

`https://<your-cluster-endpoint>:19080/Explorer`

系统会自动提示用户选择客户端证书。

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>设置远程计算机上的客户端证书
至少应有两个证书用于保护群集，一个用于保护群集和服务器证书，另一个用于保护客户端访问。  建议还使用其他辅助证书和客户端访问证书。  若要使用证书安全性来保护客户端与与群集节点之间的通信，必须先获取并安装客户端证书。 证书可以安装到本地计算机或当前用户的个人（我的）存储。  还需要服务器证书的指纹，以便客户端可以对群集进行身份验证。

运行以下 PowerShell cmdlet，在访问群集的计算机上设置客户端证书。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

如果它是自签名证书，则需要将其导入计算机的“受信任人”存储中才能使用此证书连接到安全群集。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>后续步骤

* [Service Fabric 群集升级过程和用户预期](service-fabric-cluster-upgrade.md)
* [在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric 运行状况模型简介](service-fabric-health-introduction.md)
* [应用程序安全性和 RunAs](service-fabric-application-runas-security.md)
* [Service Fabric CLI 入门](service-fabric-cli.md)
