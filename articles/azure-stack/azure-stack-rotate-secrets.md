---
title: 在 Azure Stack 中轮换机密 | Microsoft Docs
description: 了解如何在 Azure Stack 中轮换机密。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e4131bc8f038957e52b914937b2d45e670be8f5f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157269"
---
# <a name="rotate-secrets-in-azure-stack"></a>在 Azure Stack 中轮换机密

这些说明仅适用于 Azure Stack 集成系统 1803 和更高版本。请勿在低于 1802 的 Azure Stack 版本上尝试使用机密轮换

Azure Stack 使用各种机密来维持 Azure Stack 基础结构资源与服务之间的安全通信。

- **内部机密**

由 Azure Stack 基础结构使用的所有证书、密码、安全字符串和密钥，无需 Azure Stack 操作员的介入。

- **外部机密**

对外服务的基础结构服务证书，由 Azure Stack 操作员提供。 包括以下服务的证书：

- 管理员门户
- 公共门户
- 管理员 Azure 资源管理器
- 全局 Azure 资源管理器
- 管理员 KeyVault
- KeyVault
- 管理扩展主机
- ACS（包括 Blob、表和队列存储）
- ADFS *
- 关系图 *

\* 仅当环境的标识提供者是 Active Directory 联合身份验证服务 (AD FS) 才适用。

> [!Note]
> 其他所有安全密钥和字符串（包括 BMC 和交换密码以及用户和管理员帐户密码）仍然由管理员手动更新。

> [!Important]
> 从 Azure Stack 的 1811年发行版开始，机密轮换已单独划归为内部和外部证书。

若要保持 Azure Stack 基础结构的完整性，操作员需要能够定期轮换频率应与组织的安全要求相一致的其基础结构机密。

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>使用新证书颁发机构颁发的外部证书轮换机密

在以下上下文中，Azure Stack 支持使用新证书颁发机构 (CA) 颁发的外部证书进行机密轮换：

|已安装的证书 CA|要轮换到的 CA|支持|支持的 Azure Stack 版本|
|-----|-----|-----|-----|
|从自签名|到企业|不支持||
|从自签名|到自签名|不支持||
|从自签名|到公共<sup>*</sup>|支持|1803 和更高版本|
|从企业|到企业|只要客户使用与在部署时使用的相同的企业 CA，就可以支持|1803 和更高版本|
|从企业|到自签名|不支持||
|从企业|到公共<sup>*</sup>|支持|1803 和更高版本|
|从公共<sup>*</sup>|到企业|不支持|1803 和更高版本|
|从公共<sup>*</sup>|到自签名|不支持||
|从公共<sup>*</sup>|到公共<sup>*</sup>|支持|1803 和更高版本|

<sup>*</sup>指示公共证书颁发机构是指那些处于 Windows 受信任的根程序的一部分。 可以在文章中找到的完整列表[Microsoft 受信任的根证书计划：参与者（截至 2017 年 6 月 27 日）](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)。

## <a name="alert-remediation"></a>警报修正

机密过期后的 30 天内，管理员门户中会生成以下警报：

- 挂起的服务帐户密码过期
- 挂起的内部证书过期
- 挂起的外部证书过期

使用以下说明运行机密轮换将会修正这些警报。

> [!Note]
> Pre-1811年版本上的 azure Stack 环境可能会看到挂起的内部证书或密钥过期时间的警报。
> 这些警报不准确，应当忽略而无需运行内部机密轮换。
> 不准确的内部机密到期警报是在中解析 1811年 – 内部机密将过期，除非环境保持活动状态的两年的已知的问题。

## <a name="pre-steps-for-secret-rotation"></a>机密轮换前的步骤

   > [!IMPORTANT]
   > 如果已在 Azure Stack 环境中执行了机密轮换则必须为 1811年或更高版本执行再次机密轮换之前的版本更新系统。
   > 必须通过由执行机密轮换[特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)和需要 Azure Stack 操作员的凭据。
   > 如果你的 Azure Stack 操作员环境不知道是否已在环境上运行机密轮换，，更新到 1811年执行再次机密轮换前。

1. 强烈建议将 Azure Stack 实例更新到版本 1811年。

    > [!Note] 
    > Pre-1811年版本不需要轮换机密添加扩展主机证书。 应按照本文中的说明[准备适用于 Azure Stack 扩展主机](azure-stack-extension-host-prepare.md)添加扩展主机证书。

2. 在 Azure Stack 机密轮换期间，操作员可能会注意到警报打开并自动关闭。  此行为是预期行为，可以忽略警报。  运算符可以通过运行验证这些警报的有效性**Test-azurestack**。  对于使用 SCOM 监视 Azure Stack 系统的操作人员来说，将系统置于维护模式将阻止这些警报到达其 ITSM 系统，但如果 Azure Stack 系统无法访问，则将继续发出警报。

3. 在执行任何维护操作之前通知用户。 将普通的维护时间段尽量安排在非营业时间。 维护操作可能会同时影响用户工作负荷和门户操作。

    > [!Note]
    > 后续步骤仅适用于轮换 Azure Stack 外部机密。

4. 在轮换机密之前，请运行 **[Test-AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** 并确认所有测试输出都正常。
5. 准备新的替换外部证书集。 新集与 [Azure Stack PKI 证书要求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)中所述的证书规范匹配。 您可以生成证书签名请求 (CSR)，适用于购买或创建新的证书中所述的步骤[生成的 PKI 证书](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs)并准备用于在使用中的步骤在AzureStack环境中使用[准备 Azure Stack PKI 证书](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs)。 请务必验证的证书中所述的步骤准备[验证 PKI 证书](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs)。
6. 将用于轮换的证书备份存储在安全的备份位置。 如果运行轮换时发生失败，请使用备份副本替换文件共享中的证书，然后重新运行轮换。 请记得将备份副本保存在安全的备份位置。
7. 创建可从 ERCS VM 访问的文件共享。 该文件共享必须可供 **CloudAdmin** 标识读取和写入。
8. 在可以访问该文件共享的计算机上打开 PowerShell ISE 控制台。 导航到该文件共享。
9. 运行 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 创建外部证书所需的目录。

> [!IMPORTANT]
> CertDirectoryMaker 脚本将创建将遵守的文件夹结构：
>
> **.\Certificates\AAD**或 ***.\Certificates\ADFS***具体取决于你使用适用于 Azure Stack 的标识提供程序
>
> 它为您的文件夹结构结尾的最重要**AAD**或**ADFS**文件夹及所有子目录都在此结构中; 否则为**Start-secretrotation**将提供：
> ```PowerShell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> 您可以看到错误消息将指示没有访问该文件共享时出现问题，但实际上它是此处强制实施的文件夹结构。
> 详细信息可在 Microsoft AzureStack 就绪性检查器- [PublicCertHelper 模块](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)
>
> 还有一点您文件共享的文件夹结构开头**证书**文件夹否则为也将在验证失败。
> 文件共享装载应如下所示**\\ \\ \<IPAddress >\\\<共享名 >\\**并且不应包含文件夹**Certificates\AAD**或**Certificates\ADFS**内。
>
> 例如：
> - 文件共享 =  **\\ \\ \<IPAddress >\\\<共享名 >\\**
> - CertFolder = **Certificates\AAD**
> - FullPath =  **\\ \\ \<IPAddress >\\\<共享名 > \Certificates\AAD**

## <a name="rotating-external-secrets"></a>轮换外部机密

若要轮换外部机密：

1. 在新创建**\Certificates\\\<IdentityProvider >** 前期步骤中创建目录根据在目录结构中放置一组新的替换外部证书必需的证书部分中所述的格式[Azure Stack PKI 证书要求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)。

    AAD 标识提供程序的文件夹结构的示例：
    ```PowerShell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. 使用 **CloudAdmin** 帐户创建具有[特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)的 PowerShell 会话，并将会话存储为变量。 在下一步骤中要使用此变量作为参数。

    > [!IMPORTANT]  
    > 请勿输入会话，而是将会话存储为变量。

3. 运行**[调用命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)**。 将特权终结点 PowerShell 会话变量作为 **Session** 参数传递。

4. 结合以下参数运行 **Start-SecretRotation**：
    - **PfxFilesPath**  
    将网络路径指定为前面创建的 Certificates 目录。  
    - **PathAccessCredential**  
    用于访问共享的凭据的 PSCredential 对象。
    - **CertificatePassword**  
    创建的所有 pfx 证书文件使用的密码安全字符串。

5. 等待机密完成轮换。 外部机密轮换通常需要花费大约一小时。

    机密轮换成功完成后，控制台会显示“总体操作状态: 成功”。**

    > [!Note]
    > 如果机密轮换失败，请按照错误消息中的说明，然后重新运行**Start-secretrotation**与 **-请重新运行**参数。

    ```PowerShell
    Start-SecretRotation -ReRun
    ```
    如果遇到反复的机密轮换失败，请联系技术支持。

6. 成功完成机密轮换后，请从前期步骤创建的共享中删除证书，并将其存储在安全的备份位置。

## <a name="walkthrough-of-secret-rotation"></a>机密轮换的演练

以下 PowerShell 示例演示轮换机密时需要运行的 cmdlet 和参数。

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>仅轮换内部机密

> [!Note]
> 如果您怀疑内部机密受到了恶意实体，或如果你收到一个警报 （在上生成 1811年或更高版本），指示内部证书即将过期，则仅应完成内部机密轮换。
> Pre-1811年版本上的 azure Stack 环境可能会看到挂起的内部证书或密钥过期时间的警报。
> 这些警报不准确，应当忽略而无需运行内部机密轮换。
> 不准确的内部机密到期警报是在中解析 1811年 – 内部机密将过期，除非环境保持活动状态的两年的已知的问题。

1. 创建具有[特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)的 PowerShell 会话。
2. 特权终结点会话中运行**Start-secretrotation-内部**。

    > [!Note]
    > 不需要进行预 1811年版本上的 azure Stack 环境 **-内部**标志。 **Start-secretrotation**将旋转仅内部机密。

3. 等待机密完成轮换。

机密轮换成功完成后，控制台会显示“总体操作状态: 成功”。**
    > [!Note]
    > If secret rotation fails, follow the instructions in the error message and rerun **Start-SecretRotation** with the  **–Internal** and **-ReRun** parameters.  

```PowerShell
Start-SecretRotation -Internal -ReRun
```

如果遇到反复的机密轮换失败，请联系技术支持。

## <a name="start-secretrotation-reference"></a>Start-SecretRotation 参考

轮换 Azure Stack 系统的机密。 只针对 Azure Stack 特权终结点执行。

### <a name="syntax"></a>语法

#### <a name="for-external-secret-rotation"></a>对于外部机密轮换

```PowerShell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>对于内部机密轮换

```PowerShell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>重新运行外部机密轮换

```PowerShell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>重新运行的内部机密轮换

```PowerShell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>描述

**Start-secretrotation** cmdlet 轮换 Azure Stack 系统的基础结构机密。 默认情况下它会轮换所有外部网络基础结构终结点证书。 如果在使用内部的内部标志将旋转基础结构机密。 在轮换外部网络基础结构终结点， **Start-secretrotation**应具有运行**Invoke-command**与 Azure Stack 环境的脚本块的特权终结点会话传入的作为**会话**参数。

### <a name="parameters"></a>parameters

| 参数 | 类型 | 需要 | 位置 | 默认 | 描述 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | String  | False  | 名为  | 无  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享路径。 仅轮换外部机密时需要。 结尾目录必须是 **\Certificates**。 |
| CertificatePassword | SecureString | False  | 名为  | 无  | -PfXFilesPath 中提供的所有证书的密码。 如果外部机密轮换时提供了 pfxfilespath，则所需的值。 |
| 内部 | String | False | 名为 | 无 | 只要 Azure Stack 操作员希望轮换内部基础结构机密，必须使用内部标志。 |
| PathAccessCredential | PSCredential | False  | 名为  | 无  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享的 PowerShell 凭据。 仅轮换外部机密时需要。  |
| 重新运行 | SwitchParameter | False  | 名为  | 无  | 只要尝试失败后重新尝试执行机密轮换，必须使用重新运行。 |

### <a name="examples"></a>示例

#### <a name="rotate-only-internal-infrastructure-secrets"></a>轮换仅内部基础结构机密

这必须通过在 Azure Stack 运行[环境的特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)。

```PowerShell
PS C:\> Start-SecretRotation -Internal
```

此命令轮换向 Azure Stack 内部网络公开的所有基础结构机密。

#### <a name="rotate-only-external-infrastructure-secrets"></a>轮换仅外部基础结构机密  

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

此命令轮换 Azure Stack 外部网络基础结构终结点使用的 TLS 证书。

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>轮换内部和外部基础结构机密 (**pre 1811**仅)

> [!IMPORTANT]
> 此命令仅适用于 Azure Stack **pre 1811**如旋转已拆分的内部和外部证书。
>
> **从*1811年 +* 不能轮换内部和外部证书再!!!**

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

此命令轮换向 Azure Stack 内部网络公开的基础结构机密，以及用于 Azure Stack 的外部网络基础结构终结点的 TLS 证书的所有。 Start-SecretRotation 轮换堆栈生成的所有机密，由于提供了证书，因此也会轮换外部终结点证书。  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>更新基板管理控制器 (BMC) 凭据

基板管理控制器 (BMC) 监视服务器的物理状态。 规范和更新的用户帐户名和 BMC 密码的说明因原始设备制造商 (OEM) 硬件供应商。 应更新定期的 Azure Stack 组件的密码。

1. 按照 OEM 说明更新 Azure Stack 物理服务器上的 BMC。 用户帐户名和你的环境中每个 BMC 的密码必须相同。
2. 在 Azure Stack 会话中打开特权终结点。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。
3. 在 PowerShell 提示符更改为 **[IP 地址或 ERCS VM 名称]:PS>** 或 **[azs-ercs01]:PS>**（具体取决于环境）后，通过运行 `Invoke-Command` 来运行 `Set-BmcCredential`。 将特权终结点会话变量作为参数传递。 例如：

    ```PowerShell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    也可以将静态 PowerShell 版本与密码搭配使用，如以下代码行所示：

    ```PowerShell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Stack 安全性](azure-stack-security-foundations.md)
