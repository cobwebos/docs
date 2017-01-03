---
title: "对访问群集的客户端进行身份验证 | Microsoft Docs"
description: "介绍如何对访问 Service Fabric 群集的客户端进行身份验证，以及如何保护客户端与群集之间的通信。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 4ba0f864dc28beebb80567d3fac7f12cc42df677
ms.openlocfilehash: 09557bd9c83318b2bdff8ecdefedc141eb7f80db


---
# <a name="connect-to-a-secure-cluster"></a>连接到安全群集
当客户端连接到 Service Fabric 群集节点时，可以使用证书安全性或 Azure Active Directory (AAD) 与客户端建立经过身份验证的安全通信。 此身份验证可确保只有经过授权的用户才能访问该群集和部署的应用程序，以及执行管理任务。  创建群集时，必须事先在该群集上启用证书或 AAD 安全性。  有关群集安全方案的详细信息，请参阅[群集安全性](service-fabric-cluster-security.md)。 若要连接到使用证书进行保护的群集，请在将要连接到群集的计算机上[设置客户端证书](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert)。

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-cli"></a>使用 Azure CLI 连接到安全群集
下面的 Azure CLI 命令介绍如何连接到安全的群集。 

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用客户端证书连接到安全群集
证书详细信息必须与群集节点上的证书匹配。 

如果证书具有证书颁发机构 (CA)，则需要添加 `--ca-cert-path` 参数，如下例所示： 

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
如果有多个 CA，则使用逗号作为分隔符。 

如果证书中的公用名与连接终结点不匹配，则可以使用参数 `--strict-ssl-false` 跳过验证。 

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```

如果想要跳过 CA 验证，可以添加 ``--reject-unauthorized-false`` 参数，如以下命令所示：

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

若要连接到使用自签名证书进行保护的群集，请使用以下命令，去除 CA 验证和常用名验证：

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

在连接后，你应能够运行其他 CLI 命令以与群集进行交互。 

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-secure-cluster-using-powershell"></a>使用 PowerShell 连接到安全群集
通过 PowerShell 对群集执行操作之前，请先建立与群集的连接。 群集连接用于给定 PowerShell 会话中的所有后续命令。

### <a name="connect-to-an-unsecure-cluster"></a>连接到不安全的群集

若要连接到不安全的群集，请将群集终结点地址提供给 **Connect-ServiceFabricCluster** 命令：

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
运行以下 PowerShell 命令，连接到使用客户端证书对管理员访问授权的安全群集。 提供群集证书指纹，以及已被授予群集管理权限的客户端证书的指纹。 证书详细信息必须与群集节点上的证书匹配。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* 是群集节点上安装的服务器证书的指纹。 *FindValue* 是管理客户端证书的指纹。
填充参数以后，命令将如以下示例所示： 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```


<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-secure-cluster-using-the-fabricclient-apis"></a>使用 FabricClient API 连接到安全群集
Service Fabric SDK 提供用于群集管理的 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) 类。 

### <a name="connect-to-an-unsecure-cluster"></a>连接到不安全的群集

若要连接到远程的不安全群集，可直接创建 FabricClient 实例并提供群集地址：

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

对于从群集内部运行的代码（例如在可靠服务中运行的代码），可在*不* 指定群集地址的情况下创建 FabricClient。 FabricClient 可连接到当前正在运行代码的节点上的本地管理网关，避免额外的网络跃点。

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用客户端证书连接到安全群集

群集中的节点必须具有有效的证书，在 SAN 中，这些证书的公用名或 DNS 名出现在 [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) 上设置的 [RemoteCommonNames 属性](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx)中。 按此过程操作就可以在客户端与群集节点之间进行相互身份验证。

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>使用 Azure Active Directory 连接到安全群集

按此过程操作即可针对客户端标识启用 Azure Active Directory，针对服务器标识启用服务器证书。

若要使用弹出 AAD 交互登录对话框的交互模式，请执行以下操作：

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

FabricClient fc = new FabricClient(
    claimsCredentials,
    connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

若要使用不带任何人为交互的无提示模式，请执行以下操作：

（此示例依赖于 Microsoft.IdentityModel.Clients.ActiveDirectory，版本：2.19.208020213

请参阅 [Microsoft.IdentityModel.Clients.ActiveDirectory Namespace](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx)（Microsoft.IdentityModel.Clients.ActiveDirectory 命名空间），了解如何获取令牌和详细信息）

```csharp
string tenantId = "c15cfcea-02c1-40dc-8466-fbd0ee0b05d2";
string clientApplicationId = "118473c2-7619-46e3-a8e4-6da8d5f56e12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob"
    );

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";
ClaimsCredentials claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

FabricClient fc = new FabricClient(
   claimsCredentials,
   connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
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
    AuthenticationContext authContext = new AuthenticationContext(authority);

    string token = "";
    try
    {
        var authResult = authContext.AcquireToken(
            resource,
            clientId,
            new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
        token = authResult.AccessToken;
    }
    catch (AdalException ex)
    {
        Console.WriteLine("Get AccessToken failed: {0}", ex.Message);
    }

    return token;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 连接到安全群集
若要访问给定群集的 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)，请将浏览器指向：

`http://<your-cluster-endpoint>:19080/Explorer`

Azure 门户的群集基本信息窗格中也提供了完整 URL。

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>使用 Azure Active Directory 连接到安全群集

若要连接到使用 AAD 进行保护的群集，请将浏览器指向：

`https://<your-cluster-endpoint>:19080/Explorer`

系统会自动提示用户使用 AAD 登录。

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>使用客户端证书连接到安全群集

若要连接到使用证书进行保护的群集，请将浏览器指向：

`https://<your-cluster-endpoint>:19080/Explorer`

系统会自动提示用户选择客户端证书。

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>设置远程计算机上的客户端证书
至少应有两个证书用于保护群集，一个用于保护群集和服务器证书，另一个用于保护客户端访问。  建议您还使用其他辅助证书和客户端访问证书。  若要使用证书安全性来保护客户端与与群集节点之间的通信，必须先获取并安装客户端证书。 证书可以安装到本地计算机或当前用户的个人（我的）存储。  你还需要服务器证书的指纹，以便客户端可以对群集进行身份验证。

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
* [在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)。
* [Service Fabric 运行状况模型简介](service-fabric-health-introduction.md)
* [应用程序安全性和 RunAs](service-fabric-application-runas-security.md)




<!--HONumber=Nov16_HO4-->


