<properties
   pageTitle="保护 Azure Service Fabric 中有状态服务的复制流量"
   description="启用复制后，有状态服务会将其状态从主副本复制到次要副本，需保护此类流量以防窃听和篡改。"
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="04/13/2015"
   wacn.date=""/>

# 保护复制流量

启用复制后，有状态服务将跨多个副本复制状态。本页介绍如何对此类流量配置保护。

支持以下两种类型的安全设置：

- X509：使用 X509 证书来保护通信通道。用户应使用 ACL 证书私钥，以便允许参与者/服务主机进程使用证书凭据。
- Windows：使用 Windows 安全性（需要 Active Directory）来保护通信通道。

以下部分说明如何配置上述两种类型的设置。配置“CredentialType”决定使用哪种类型。

## CredentialType=X509

### 配置名称

|Name|备注|
|----|-------|
|StoreLocation|用于查找证书的存储位置：CurrentUser 或 LocalMachine|
|StoreName|用于查找证书的存储名称|
|FindType|标识 FindValue 参数提供的值的类型：FindBySubjectName 或 FindByThumbPrint|
|FindValue|用于查找证书的搜索目标|
|AllowedCommonNames|复制器所使用的证书公用名/DNS 名称的逗号分隔列表。|
|IssuerThumbprints|颁发者证书指纹的逗号分隔列表。指定该项时，将验证传入证书是否由该列表中的某个颁发者颁发。始终执行链验证。|
|RemoteCertThumbprints|复制器所使用的证书指纹的逗号分隔列表。|
|ProtectionLevel|指示如何保护数据：Sign、EncryptAndSign 或无|

## CredentialType=Windows

### 配置名称

|Name|备注|
|----|-------|
|ServicePrincipalName|为服务注册的服务主体名称。如果服务/参与者主机进程以计算机帐户身份（例如：NetworkService、LocalSystem 等等）运行，则该项可以为空|
|WindowsIdentities|所有服务/参与者主机进程的 Windows 标识的逗号分隔列表。
|ProtectionLevel|指示如何保护数据：Sign、EncryptAndSign 或无|

## 示例

### 示例 1：CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### 示例 2：CredentialType=Windows
此代码段演示当所有服务/参与者主机进程都以 NetworkService 或 LocalSystem 身份运行时的示例。ServicePrincipalName 为空。

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### 示例 3：CredentialType=Windows
此代码段演示当所有服务/参与者主机进程都以具有有效 ServicePrincipalName 的组托管服务帐户身份运行时的示例。

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=74-->