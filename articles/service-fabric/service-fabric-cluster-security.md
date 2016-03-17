<properties
   pageTitle="保护 Service Fabric 群集 | Microsoft Azure"
   description="如何保护 Service Fabric 群集。有哪些选项？"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="02/01/2016"
   wacn.date=""/>

# 保护 Service Fabric 群集

Azure Service Fabric 群集是你拥有的资源。为了防止未经授权访问资源，你必须保护资源，尤其是其中有正在运行的生产工作负荷时。本文将逐步指导你完成保护 Service Fabric 群集的过程。

## 群集安全方案

Service Fabric 针对以下方案提供安全性：

1. **节点到节点的安全性：**保护 VM 与群集中计算机之间的通信。这可确保只有已获授权加入群集的计算机可以参与托管应用程序和群集中的服务。

	![节点到节点通信示意图][Node-to-Node]

2. **客户端到节点的安全性：**保护 Service Fabric 客户端与群集中单个节点之间的通信。这种类型的安全性将验证并保护客户端通信，确保只有已获授权的用户可以访问群集与群集上部署的应用程序。客户端通过其 Windows 安全性凭据或其证书安全性凭据进行唯一标识。

	![客户端到节点通信示意图][Client-to-Node]

	对于节点到节点的安全性或客户端到节点的安全性，都可以使用[证书安全性](https://msdn.microsoft.com/zh-cn/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/zh-cn/library/ff649396.aspx)。节点到节点的安全性或客户端到节点的安全性彼此独立，因此两者可能相同或不同。

	当你创建群集时，Azure Service Fabric 将使用指定为节点类型配置一部分的 X.509 服务器证书。本文末尾概述了这些证书是什么，以及如何获取或创建这些证书。

3. **基于角色的访问控制 (RBAC)：**限制只有一组特定证书可以在群集上执行管理操作。

## 使用证书保护 Service Fabric 群集。

若要设置安全的 Service Fabric 群集，至少需要一个服务器 X.509 证书，你要将它上载到 Azure 密钥保管库并在创建群集的过程中用于。

共有三个不同的步骤：

1. 获取 X.509 证书。
2. 将证书上载到 Azure 密钥保管库。
3. 将证书的位置和详细信息提供给 Service Fabric 群集创建过程。

### 步骤 1：获取 X.509 证书

1. 对于运行生产工作负荷的群集，必须使用[证书颁发机构 (CA)](https://zh.wikipedia.org/wiki/Certificate_authority) 签名的 X.509 证书来保护群集。有关如何获取这些证书的详细信息，请转到[如何：获取证书](http://msdn.microsoft.com/zh-cn/library/aa702761.aspx)。
2. 对于仅用于测试的群集，可以选择使用自签名证书。下面的步骤 2.5 说明如何执行该操作。

### 步骤 2：将 X.509 证书上载到密钥保管库

这是一个复杂的过程，因此我们要将一个 PowerShell 模块上载到 Git 存储库，它将为完成此过程。

**步骤 2.1**：将此文件夹从[此 Git 存储库](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)复制到计算机。

**步骤 2.2**：确保 Azure PowerShell 1.0+ 已安装在计算机上。如果尚未安装，我们强烈建议你根据[如何安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure) 一文中所述的步骤安装。

**步骤 2.3**：打开 PowerShell 窗口并导入 ServiceFabricRPHelpers.psm。（这是你在步骤 2.1 中下载的模块）。

```
Remove-Module ServiceFabricRPHelpers
```

复制以下示例，并将路径更改为 .psm1 以符合计算机上的路径。

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**步骤 2.4**：如果要使用以前获取的证书，请遵循此步骤中的过程。否则，请跳到步骤 2.5，该步骤说明了如何创建自签名证书，并将自签名证书部署到密钥保管库。

登录到你的 Azure 帐户。如果此 PowerShell 命令由于某些原因而失败，你应该检查 Azure PowerShell 是否已正确安装。

```
Login-AzureRmAccount
```

以下脚本将创建新的资源组和/或密钥保管库（如果尚不存在）。

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
以下是已填充脚本的示例。

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

该脚本成功完成时，你将看到类似于下面的输出，执行步骤 3（配置安全群集）时将用到这些数据。

- **证书指纹**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

- **SourceVault** /密钥保管库的资源 ID: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **证书 URL** /密钥保管库中的证书位置 URL: https://chackdankeyvalut4doc.vault.chinacloudapi.cn:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea

现在你已拥有设置安全群集所需的信息。请转到步骤 3。

**步骤 2.5**：如果你*没有*证书，但想要创建新的自签名证书并将它上载到密钥保管库，请执行以下步骤。

登录到你的 Azure 帐户。如果此 PowerShell 命令由于某些原因而失败，你应该检查 Azure PowerShell 是否已正确安装。

```
Login-AzureRmAccount
```

以下脚本将创建新的资源组和/或密钥保管库（如果尚不存在）。

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

提供给脚本的 OutputPath 将包含脚本上载到密钥保管库的新自签名证书。

>[AZURE.NOTE] DnsName 字符串指定一个或多个 DNS 名称，以便在 CloneCert 参数中未指定要复制的证书时放入证书的使用者可选名称扩展中。第一个 DNS 名称还将保存为使用者名称。如果未指定任何签名证书，则第一个 DNS 名称还将保存为颁发者名称。

有关如何创建自签名证书的详细信息，请访问 [https://technet.microsoft.com/zh-cn/library/hh848633.aspx](https://technet.microsoft.com/zh-cn/library/hh848633.aspx)。

以下是已填充脚本的示例。

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

因为它是自签名证书，因此需要将其导入计算机的“受信任人”存储中才能使用此证书连接到安全群集。

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

脚本成功完成时，你将看到类似于下面的输出。执行步骤 3 时需要用到这些数据。

- **证书指纹**: 64881409F4D86498C88EEC3697310C15F8F1540F

- **SourceVault** /密钥保管库的资源 ID: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **证书 URL** /密钥保管库中的证书位置 URL: https://chackdankeyvalut4doc.vault.chinacloudapi.cn:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### 步骤 3：设置安全群集

请遵循 [Service Fabric 群集创建过程](/documentation/articles/service-fabric-cluster-creation-via-portal)中所述的步骤，直到进入“安全配置”部分。然后跳到此处显示的说明来设置安全配置：

>[AZURE.NOTE]
所需的证书在“安全配置”下的“节点类型”级别指定。必须为群集中的每个节点类型指定此配置。尽管本文档演练如何使用门户执行此操作，但你可以使用 Azure Resource Manager 模板来实现相同的目的。

![Azure 门户中“安全配置”的屏幕截图][SecurityConfigurations_01]

必需参数：

- **安全模式。** 选择“X509 证书”。向 Service Fabric 指出你想要配置安全群集。
- **群集保护级别。** 请参阅此[保护级别文档](https://msdn.microsoft.com/zh-cn/library/aa347692.aspx)，了解每个值的含义。尽管我们允许在此处使用三个值（EncryptAndSign、Sign 和 None），但除非你知道的意图，否则最好保留默认值 EncryptAndSign。
- **源保管库。** 这是密钥保管库的资源 ID。其格式应该是：

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **证书 URL。** 这是密钥保管库中证书所上载到的位置 URL。其格式应该是：

    ```
    https://<name of the vault>.vault.chinacloudapi.cn:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.chinacloudapi.cn:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **证书指纹。** 这是证书的指纹，可以在前面指定的 URL 中找到。

可选参数：

 - 可以选择性地指定客户端计算机的、用于在群集上执行操作的其他证书。默认情况下，在必需参数中指定的指纹将添加到能够执行客户端操作的已授权指纹列表中。

管理客户端：此信息用于验证连接到群集管理终结点的客户端是否可以提供正确的凭据，以便在群集上执行管理和只读操作。你可以指定多个想要授权其执行管理操作的证书。

- **授权依据。** 向 Service Fabric 指出是要使用使用者名称还是指纹来查找此证书。使用使用者名称授权不是良好的安全实践，但是可以增加操作的弹性。
- **使用者名称。** 仅当已指定按使用者名称授权时才需要。
- **颁发者指纹**当客户端向服务器提供自己的凭据时，能够让服务器执行额外的检查。

只读客户端：此信息用于验证连接到群集管理终结点的客户端是否可以提供正确的凭据，以便在群集上执行只读操作。你可以指定多个想要授权其执行只读操作的证书。

- **授权依据。** 向 Service Fabric 指出是要使用使用者名称还是指纹来查找此证书。使用使用者名称授权不是良好的安全实践，但是可以增加操作的弹性。
- **使用者名称。** 仅当已指定按使用者名称授权时才需要。
- **颁发者指纹。** 当客户端向服务器提供自己的凭据时，能够让服务器执行额外的检查。

## 更新群集中的证书

Service Fabric 可让你指定两个证书（主证书和辅助证书）。默认情况下，在创建时所指定的证书是主证书。若要添加另一个证书，你必须将该证书部署到群集中的 VM。上述步骤 2 概述了如何将新证书上载到密钥保管库。可以使用与第一个证书相同的密钥保管库。有关详细信息，请参阅[将证书从客户管理的密钥保管库部署到 VM](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)。

完成该操作后，使用门户或 Resource Manager，向 Service Fabric 指出你有一个同样可供使用的辅助证书。只需要指纹。

下面是添加辅助证书的过程：

1. 转到门户并浏览到想要添加此证书的群集资源。
2. 在“设置”下面，单击证书配置并输入辅助证书指纹。
3. 单击“保存”。随即将开始部署，在部署成功完成后，你可以使用主证书或辅助证书在群集上执行管理操作。

![门户中证书指纹的屏幕截图][SecurityConfigurations_02]

下面是删除旧证书，以使群集不使用它的过程：

1. 转到门户并导航到群集的安全设置。
2. 删除其中的一个证书。
3. 单击“保存”，随即将开始部署。部署完成后，删除的证书不再可用于连接到群集。

>[AZURE.NOTE] 对于安全群集，始终需要至少部署一个有效（未吊销或过期）的证书（主要或辅助），否则将无法访问群集。


## Service Fabric 使用的证书类型

### X.509 证书

X.509 数字证书通常用于验证客户端与服务器，以及对消息进行加密和数字签名。有关这些证书的详细信息，请参阅 MSDN 库中的[使用证书](http://msdn.microsoft.com/zh-cn/library/ms731899.aspx)。

>[AZURE.NOTE]
- 运行生产工作负荷的群集中使用的证书应使用正确配置的 Windows Server 证书服务来创建，或者从已批准的[证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 获取。
- 切勿在生产环境中使用通过 MakeCert.exe 等工具创建的临时或测试证书。
- 对于用于测试的群集，可以选择使用自签名证书。

### 服务器证书和客户端证书

#### 服务器 X.509 证书

服务器证书的主要任务是在客户端上对服务器（节点）进行身份验证，或者在一个服务器（节点）上对另一个服务器（节点）进行身份验证。客户端或节点对节点进行身份验证时，一项初始检查是检查“使用者”字段中的公用名值。此公用名或某个证书的使用者可选名称必须存在于允许的公用名列表中。

以下文章说明了如何生成包含使用者可选名称 (SAN) 的证书：
[如何将使用者可选名称添加到安全的 LDAP 证书](http://support.microsoft.com/zh-cn/kb/931351)。

>[AZURE.NOTE] “使用者”字段可以包含多个值，每个值的前面带有代表该值类型的首字母。最常见的首字母是“CN”，表示公用名，例如“CN = www.contoso.com”。“使用者”字段也可能是空白的。如果可选的“使用者可选名称”字段已填充数据，则此字段必须包含证书的公用名，以及每个使用者可选名称的一个条目。这些内容作为“DNS 名称”值输入。

证书的“预期目的”字段值应包含适当的值，例如“服务器身份验证”或“客户端身份验证”。

#### 客户端证书

客户端证书通常不由第三方证书颁发机构颁发。当前用户位置的“个人”存储通常包含由根证书颁发机构放置的客户端证书，其预期目的是“客户端身份验证”。客户端可以在需要相互身份验证时使用此类证书。

>[AZURE.NOTE] Service Fabric 群集上的所有管理操作都需要服务器证书。客户端证书不能用于管理。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 后续步骤

- [Service Fabric 群集升级过程与期望](/documentation/articles/service-fabric-cluster-upgrade)
- [在 Visual Studio 中管理 Service Fabric 应用程序](/documentation/articles/service-fabric-manage-application-in-visual-studio)。
- [Service Fabric 运行状况模型简介](/documentation/articles/service-fabric-health-introduction)
- [应用程序安全性和 RunAs](/documentation/articles/service-fabric-application-runas-security)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=Mooncake_0307_2016-->