---
title: 在 Azure Stack 中轮换机密 | Microsoft Docs
description: 了解如何在 Azure Stack 中轮换机密。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 509570dfe0e3d4be2e589ac1958dd377dc4e8e03
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>在 Azure Stack 中轮换机密

*这些说明仅适用于 Azure 堆栈集成系统版本 1803年和更高版本。不要尝试在前 1802 Azure 堆栈版本上的机密旋转*

Azure 堆栈使用各种机密维护 Azure 堆栈基础结构资源和服务之间安全通信。

- **内部机密**  
所有证书、 密码、 安全字符串和 Azure 堆栈基础结构而无需干预的 Azure 堆栈运算符所使用的密钥。 

- **外部机密**  
由 Azure 堆栈运营商提供的面向外部的服务的基础结构服务证书。 这包括以下服务的证书： 
    - 管理员门户 
    - 公共门户 
    - 管理员 Azure 资源管理器 
    - 全局 Azure 资源管理器 
    - 管理员 Keyvault 
    - KeyVault 
    - ACS （包括 blob、 表和队列存储） 
    - ADFS<sup>*</sup>
    - Graph<sup>*</sup>

    > <sup>*</sup> 仅当环境的标识提供程序是 Active Directory 联合服务 (AD FS) 才适用。

> [!NOTE]
> 所有其他安全密钥和字符串，包括 BMC 并切换密码，管理员仍手动更新用户和管理员帐户密码。 

若要保持 Azure 堆栈基础结构的完整性，运算符需要定期更改其基础结构在其组织的安全要求与相一致的频率的机密信息的能力。

## <a name="alert-remediation"></a>警报修正

当机密过期的 30 天内，在管理员门户中生成的以下警报： 

- 挂起的服务帐户密码过期 
- 挂起的内部证书过期 
- 挂起的外部证书过期 

运行使用下面的说明的机密旋转将修正这些警报。

## <a name="pre-steps-for-secret-rotation"></a>密钥轮换的预先步骤

1.  通知你的用户的任何维护操作。 计划正常的维护时段，尽可能多地，非工作时间期间。 维护操作可能会同时影响用户工作负荷和门户操作。

    > [!note]  
    > 下一步的步骤仅适用时轮换 Azure 堆栈外部机密。

2.  准备一组新的替换外部证书。 新的设置与匹配中所述的证书规范[Azure 堆栈 PKI 证书要求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)。
3.  将存储备份到用于在安全的备份位置旋转的证书。 如果你旋转运行然后失败，将证书文件共享中的替换的备份副本之前重新运行旋转。 请注意，将备份副本保存在安全的备份位置。
3.  创建文件共享，你可以从 ERCS Vm 访问。 文件共享必须可读和可写的**CloudAdmin**标识。
4.  从有权访问文件共享的位置的计算机打开 PowerShell ISE 控制台。 导航到你的文件共享。 
5.  运行**[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)**创建所需的目录的外部证书。

## <a name="rotating-external-and-internal-secrets"></a>旋转的外部和内部机密

要旋转这两个外部内部机密：

1. 在新创建**/证书**中预先步骤中，创建目录的目录结构根据必需证书一节中介绍的格式将替换外部证书新组[Azure 堆栈 PKI 证书要求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)。
2. 创建一个 PowerShell 会话[特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)使用**CloudAdmin**帐户和存储变量的会话。 你将使用此变量作为下一步中的参数。

    > [!IMPORTANT]  
    > 请勿在输入会话，存储变量的会话。
    
3. 运行**[调用命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**。 将为你特权终结点 PowerShell 会话变量传递**会话**参数。 
4. 运行**开始 SecretRotation**使用以下参数：
    - **PfxFilesPath**  
    指定你前面创建的证书目录的网络路径。  
    - **PathAccessCredential**  
    一个用于到共享的凭据的 PSCredential 对象。 
    - **CertificatePassword**  
    用于所有创建的 pfx 证书文件的密码安全字符串。
4. 您的机密信息旋转稍候。  
机密旋转成功完成时，将显示你的控制台**总体操作状态： 成功**。 
5. 成功完成后的机密旋转，从预步骤中创建的共享中删除你的证书并将它们存储在其安全的备份位置。 

## <a name="walkthrough-of-secret-rotation"></a>演练中的机密的旋转

下面的 PowerShell 示例演示的 cmdlet 和参数来运行，以便将您的机密信息。

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = "CertPasswordHere" | ConvertTo-SecureString
$CertShareCred = Get-Credential 
$CertSharePath = <NetworkPathofCertShare>   
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>轮换仅内部机密

轮换 Azure 堆栈的内部机密：

1. 创建一个 PowerShell 会话[特权终结点](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)。
2. 在特权的终结点会话中，运行**开始 SecretRotation**不带任何参数。

## <a name="start-secretrotation-reference"></a>开始 SecretRotation 引用

旋转 Azure 堆栈系统的机密。 仅针对 Azure 堆栈特权终结点执行。

### <a name="syntax"></a>语法

路径 （默认值）

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>说明

开始 SecretRotation cmdlet 将旋转 Azure 堆栈系统的基础结构机密。 默认情况下，它将旋转到内部基础结构网络公开的所有机密，与用户输入其还将都旋转的所有外部网络基础结构终结点的证书。 轮换外部网络基础结构终结点，应开始 SecretRotation 通过 Invoke-command 脚本块中执行与作为会话参数传递的 Azure 堆栈环境的特权的终结点会话中。
 
### <a name="parameters"></a>parameters

| 参数 | Type | 需要 | 位置 | 默认 | 说明 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | String  | False  | 名为  | 无  | 文件共享路径**\Certificates**目录包含所有外部网络终结点证书。 仅在轮换内部和外部机密时需要。 必须是目录结尾**\Certificates**。 |
| CertificatePassword | SecureString | False  | 名为  | 无  | -PfXFilesPath 中提供的所有证书的密码。 如果 PfxFilesPath 提供内部和外部的机密转动时所需的值。 |
|

### <a name="examples"></a>示例
 
**旋转仅内部基础结构机密**

```powershell  
PS C:\> Start-SecretRotation  
```

此命令将旋转所有面临 Azure 堆栈的内部网络的基础结构机密。 开始 SecretRotation 旋转所有堆栈生成机密，但由于没有任何提供的证书，因此将不 rotated 外部终结点证书。  

**旋转内部和外部的基础结构机密**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

此命令将旋转面临 Azure 堆栈的内部网络的基础结构机密，以及用于 Azure 堆栈的外部网络基础结构终结点的 TLS 证书的所有。 开始 SecretRotation 旋转所有堆栈生成机密、，因为存在提供的证书，还将 rotated 外部终结点证书。  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>更新基板管理控制器 (BMC) 密码

基板管理控制器 (BMC) 监视你的服务器的物理状态。 有关更新 BMC 密码的规范和说明会根据原始设备制造商 (OEM) 硬件供应商而有所不同。 你应更新你的密码的正则节奏 Azure 堆栈组件。

1. 按照 OEM 说明在 Azure Stack 的物理服务器上更新 BMC。 环境中每个 BMC 的密码必须相同。
2. 在 Azure Stack 会话中打开特权终结点。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。
3. 在 PowerShell 提示符更改为 **[IP 地址或 ERCS VM 名称]: PS>** 或更改为 **[azs-ercs01]: PS>**（取决于环境）后，通过运行 `invoke-command` 来运行 `Set-BmcPassword`。 将特权终结点会话变量作为参数传递。 例如：

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    也可以将静态 PowerShell 版本与密码搭配使用，如以下代码行所示：
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>后续步骤

[了解有关 Azure 堆栈安全的详细信息](azure-stack-security-foundations.md)
