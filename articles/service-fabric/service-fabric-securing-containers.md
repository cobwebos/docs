---
title: "Azure Service Fabric 容器安全性 | Microsoft Docs"
description: "现在了解如何保护容器服务。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: cc3095d9067ea529c565cc66a5e503218c822a39
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="container-security"></a>容器安全性

Service Fabric 提供一种机制，供容器内服务访问在 Windows 或 Linux 群集（5.7 或更高版本）的节点中安装的证书。 此外，Service Fabric 还支持 Windows 容器的 gMSA（组托管服务帐户）。 

## <a name="certificate-management-for-containers"></a>容器的证书管理

可以通过指定证书保护容器服务。 该证书必须安装在群集的所有节点上的 LocalMachine 中。 `ContainerHostPolicies` 标记下的应用程序清单中提供了证书信息，如以下代码片段所示：

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

对于 Windows 群集，运行时在启动应用程序时读取证书，并为每个证书生成 PFX 文件和密码。 可在容器内使用以下环境变量访问此 PFX 文件和密码： 

* **Certificates_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificates_ServicePackageName_CodePackageName_CertName_Password**

对于 Linux 群集，证书 (PEM) 只是从 X509StoreName 指定的存储中复制到容器中。 对应的 linux 环境变量为：

* **Certificates_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey**

或者，如果已有所需形式的证书且只想在容器内访问它，可在应用包内创建数据包，并在应用程序清单中指定以下内容：

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

容器服务或进程负责将证书文件导入到容器中。 要导入证书，可以在容器进程内使用 `setupentrypoint.sh` 脚本或执行自定义代码。 用于导入 PFX 文件的 C# 示例代码如下所示：

```csharp
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
此 PFX 证书可以用于对应用程序或服务或与其他服务的安全通信进行身份验证。 默认情况下，文件仅可列入 SYSTEM 的 ACL。 根据服务需要，可将其列入其他帐户的 ACL。


## <a name="set-up-gmsa-for-windows-containers"></a>设置 Windows 容器的 gMSA

若要设置 gMSA（组托管服务帐户），凭据规范文件 (`credspec`) 会放置在群集的所有节点上。 可以在所有节点上使用 VM 扩展复制文件。  `credspec` 文件必须包含 gMSA 帐户信息。 有关 `credspec` 文件的详细信息，请参阅[服务帐户](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts)。 在应用程序清单中指定凭据规范和 `Hostname` 标记。 `Hostname` 标记必须匹配容器在其下运行的 gMSA 帐户名称。  `Hostname` 标记使容器本身能够使用 Kerberos 身份验证向域中的其他服务进行身份验证。  应用程序清单中用于指定 `Hostname` 和 `credspec` 的示例在以下代码片段中显示：

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>后续步骤

* [将 Windows 容器部署到 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [将 Docker 容器部署到 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)
