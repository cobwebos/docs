---
title: 使用证书在 Windows 上保护群集
description: 保护 Azure Service Fabric 独立群集或本地群集内部的通信，以及客户端与群集之间的通信。
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: cf7d418d8bca8f690acf29ba701fdc54ced1ca6c
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561992"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>使用 X.509 证书在 Windows 上保护独立群集
本文介绍如何保护独立 Windows 群集的不同节点之间的通信。 此外，还介绍如何使用 X.509 证书针对连接到此群集的客户端进行身份验证。 身份验证可确保只有经过授权的用户才能访问该群集和部署的应用程序，以及执行管理任务。 创建群集时，应在该群集上启用证书安全性。  

若要详细了解群集安全性（如节点到节点安全性、客户端到节点安全性和基于角色的访问控制），请参阅[群集安全方案](service-fabric-cluster-security.md)。

## <a name="which-certificates-do-you-need"></a>需要哪些证书？
首先[下载适用于 Windows Server 的 Service Fabric 包](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package)，将其下载到群集中的某个节点。 在下载的包中，可以找到 ClusterConfig.X509.MultiMachine.json 文件。 打开该文件，并查看 properties 部分下面的 security 部分：

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

该部分描述保护独立 Windows 群集所需的证书。 如果指定群集证书，请将 ClusterCredentialType 的值设置为 _X509_。 如果为外部连接指定服务器证书，请将 ServerCredentialType 的值设置为 _X509_。 虽然并非强制，但为了妥善保护群集，我们建议同时拥有这两个证书。 如果将这些值设置为 *X509*，还必须指定相应的证书，否则 Service Fabric 将引发异常。 在某些情况下，仅需要指定 _ClientCertificateThumbprints_ 或 _ReverseProxyCertificate_。 在这些情况下，不需要将 _ClusterCredentialType_ 或 _ServerCredentialType_ 设置为 _X509_。


> [!NOTE]
> [指纹](https://en.wikipedia.org/wiki/Public_key_fingerprint)是证书的主要标识。 若要查找创建的证书的指纹，请阅读[检索证书的指纹](https://msdn.microsoft.com/library/ms734695.aspx)。
> 
> 

下表列出了在设置群集时所需的证书：

| **CertificateInformation 设置** | **说明** |
| --- | --- |
| ClusterCertificate |建议用于测试环境。 需要使用此证书保护群集上节点之间的通信。 可以使用两个不同的证书（一个主证书和一个辅助证书）进行升级。 在 Thumbprint 部分设置主证书的指纹，在 ThumbprintSecondary 变量中设置辅助证书的指纹。 |
| ClusterCertificateCommonNames |建议用于生产环境。 需要使用此证书保护群集上节点之间的通信。 可以使用一个或两个群集证书公用名称。 CertificateIssuerThumbprint 对应此证书的颁发者的指纹。 如果要使用具有相同常见名称的多个证书，可以指定多个颁发者指纹。|
| ClusterCertificateIssuerStores |建议用于生产环境。 此证书与群集证书的颁发者相对应。 可以在此节下提供颁发者公用名称和相应的存储名称，而不是在 ClusterCertificateCommonNames 下指定颁发者指纹。  这样可以轻松滚动更新群集颁发者证书。 如果使用多个群集证书，可以指定多个颁发者。 空的 IssuerCommonName 会将 X509StoreNames 下指定的相应存储中的所有证书加入允许列表。|
| ServerCertificate |建议用于测试环境。 当客户端尝试连接到此群集时，系统会向客户端提供此证书。 为方便起见，可以选择对 ClusterCertificate 和 ServerCertificate 使用相同的证书。 可以使用两个不同的服务器证书（一个主证书和一个辅助证书）进行升级。 在 Thumbprint 部分设置主证书的指纹，在 ThumbprintSecondary 变量中设置辅助证书的指纹。 |
| ServerCertificateCommonNames |建议用于生产环境。 当客户端尝试连接到此群集时，系统会向客户端提供此证书。 CertificateIssuerThumbprint 对应此证书的颁发者的指纹。 如果要使用具有相同常见名称的多个证书，可以指定多个颁发者指纹。 为方便起见，可以选择对 ClusterCertificateCommonNames 和 ServerCertificateCommonNames 使用相同的证书。 可以使用一个或两个服务器证书公用名称。 |
| ServerCertificateIssuerStores |建议用于生产环境。 此证书与服务器证书的颁发者相对应。 可以在此节下提供颁发者公用名称和相应的存储名称，而不是在 ServerCertificateCommonNames 下指定颁发者指纹。  这样可以轻松滚动更新服务器颁发者证书。 如果使用多个服务器证书，可以指定多个颁发者。 空的 IssuerCommonName 会将 X509StoreNames 下指定的相应存储中的所有证书加入允许列表。|
| ClientCertificateThumbprints |在经过身份验证的客户端上安装这一组证书。 可以在想要允许其访问群集的计算机上安装许多不同的客户端证书。 在 CertificateThumbprint 变量中设置每个证书的指纹。 如果将 IsAdmin 设置为 *true*，则安装了此证书的客户端可以针对群集执行管理员管理活动。 如果 IsAdmin 设置为 *false*，则使用此证书的客户端只能执行用户有权执行的操作（通常为只读）。 有关角色的详细信息，请阅读[基于角色的访问控制 (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac)。 |
| ClientCertificateCommonNames |在 CertificateCommonName 中设置第一个客户端证书的通用名称。 CertificateIssuerThumbprint 是此证书的颁发者的指纹。 若要详细了解公用名称和颁发者，请阅读[使用证书](https://msdn.microsoft.com/library/ms731899.aspx)。 |
| ClientCertificateIssuerStores |建议用于生产环境。 此证书与客户端证书的颁发者（管理员和非管理员角色）相对应。 可以在此节下提供颁发者公用名称和相应的存储名称，而不是在 ClientCertificateCommonNames 下指定颁发者指纹。  这样可以轻松滚动更新客户端颁发者证书。 如果使用多个客户端证书，可以指定多个颁发者。 空的 IssuerCommonName 会将 X509StoreNames 下指定的相应存储中的所有证书加入允许列表。|
| ReverseProxyCertificate |建议用于测试环境。 如果想要保护[反向代理](service-fabric-reverseproxy.md)，可以选择指定此证书。 若要使用此证书，请确保已在 nodeTypes 中设置了 reverseProxyEndpointPort。 |
| ReverseProxyCertificateCommonNames |建议用于生产环境。 如果想要保护[反向代理](service-fabric-reverseproxy.md)，可以选择指定此证书。 若要使用此证书，请确保已在 nodeTypes 中设置了 reverseProxyEndpointPort。 |

下面是群集配置示例，其中提供了群集证书、服务器证书和客户端证书。 对于群集/服务器/reverseProxy 证书，不能将指纹和公用名同时配置为相同的证书类型。

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>证书滚动更新
使用证书公用名而不使用指纹时，证书滚动更新不需要群集配置升级。 对于颁发者指纹升级，请确保新的指纹列表具有与旧列表的交集。 首先必须使用新的颁发者指纹进行配置升级，然后在存储中安装新证书（群集/服务器证书和颁发者证书）。 请在安装新的颁发者证书后，将旧的颁发者证书在证书存储中至少保存两小时。
如果使用颁发者存储，则不需要对颁发者证书滚动更新执行配置升级。 在相应的证书存储中安装新的具有更迟到期日期的颁发者证书并在几个小时后删除旧的颁发者证书。

## <a name="acquire-the-x509-certificates"></a>获取 X.509 证书
若要保护群集内部的通信，首先需要获取群集节点的 X.509 证书。 此外，如果只想允许经过授权的计算机/用户连接到此群集，需要获得并安装客户端计算机的证书。

对于运行生产工作负荷的群集，请使用[证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 签名的 X.509 证书来保护群集。 有关如何获取这些证书的详细信息，请参阅[如何获取证书](https://msdn.microsoft.com/library/aa702761.aspx)。

对于用于测试的群集，可以选择使用自签名证书。

## <a name="optional-create-a-self-signed-certificate"></a>可选：创建自签名证书
创建能够得到适当保护的自签名证书的一种方法是，使用位于 C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure 目录的 Service Fabric SDK 文件夹中的 CertSetup.ps1 脚本。 编辑此文件以更改证书的默认名称。 （查找值 CN=ServiceFabricDevClusterCert。）以 `.\CertSetup.ps1 -Install` 的方式运行此脚本。

现在，请将证书导出到使用受保护密码的 .pfx 文件。 首先获取证书的指纹。 
1. 从“开始”  菜单，运行“管理计算机证书”  。 

2. 转到 **Local Computer\Personal** 文件夹，找到创建的证书。 

3. 双击证书将其打开，选择“详细信息”选项卡，然后向下滚动到“指纹”字段   。 

4. 删除空格，将指纹值复制到以下 PowerShell 命令中。 

5. 将 `String` 值更改为一个合适的安全密码来保护它，然后在 PowerShell 中运行以下内容：

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. 若要查看安装在计算机上的证书的详细信息，请运行以下 PowerShell 命令：

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

此外，如果你有 Azure 订阅，请按照[使用 Azure 资源管理器创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)中的步骤进行操作。

## <a name="install-the-certificates"></a>安装证书
获取证书后，可将其安装在群集节点上。 节点上需安装最新的 Windows PowerShell 3.x。 每个节点上，针对群集证书和服务器证书以及任何辅助证书重复这些步骤。

1. 将 .pfx 文件复制到节点。

2. 以管理员身份打开 PowerShell 窗口并输入以下命令。 将 *$pswd* 替换成在创建此证书时使用的密码。 将 *$PfxFilePath* 替换为复制到此节点的 .pfx 文件的完整路径。
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. 现在通过运行以下脚本设置对此证书的访问控制，以便在网络服务帐户下运行的 Service Fabric 进程可以使用它。 为服务帐户提供证书指纹以及**网络服务**。 可检查证书上的 ACL 是否正确，方法是在“开始”   > “管理计算机证书”  中打开证书，并查看“所有任务”   > “管理私钥”  。
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. 针对每个服务器证书重复上述步骤。 还可以使用这些步骤在需要让其访问群集的计算机上安装客户端证书。

## <a name="create-the-secure-cluster"></a>创建安全群集
配置 ClusterConfig.X509.MultiMachine.json 文件的 security 部分后，可以继续阅读[创建群集](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster)部分，配置节点并创建独立群集。 创建群集时，请务必使用 ClusterConfig.X509.MultiMachine.json 文件。 例如，命令可能如下所示：

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

成功运行安全的独立 Windows 群集，并设置可连接到该群集的经过身份验证的客户端之后，请遵循[使用 PowerShell 连接群集](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell)部分中的步骤连接到该群集。 例如：

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

然后可以运行其他 PowerShell 命令来使用此群集。 例如，可以运行 [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) 显示此安全群集上的节点列表。


若要删除群集，请连接到已下载 Service Fabric 包的群集上的节点、打开命令行，并转到包文件夹。 现在运行以下命令：

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> 证书配置不正确会导致在部署时看不到群集。 若要对安全性问题进行自我诊断，请查看事件查看器组（**应用程序和服务日志** > **Microsoft-Service Fabric**）。
> 
> 

