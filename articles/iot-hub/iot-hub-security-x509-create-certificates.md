---
title: 如何使用 PowerShell 创建 X.509 证书 | Microsoft Docs
description: 如何使用 PowerShell 在本地创建 X.509 证书，以及在模拟环境下在 Azure IoT 中心启用基于 X.509 的安全性。
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: dkshir
ms.openlocfilehash: d0063ff79a0bda88fffb486f03286f6784ece7fa
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "34637593"
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>用于管理 CA 签名的 X.509 证书的 PowerShell 脚本

IoT 中心基于 X.509 证书的安全性需从 [X.509 证书链](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)开始，其中包括根证书、任何中间证书，及至叶证书。 此操作说明指南演示使用 [OpenSSL](https://www.openssl.org/) 创建 X.509 证书并对其进行签名的示例 PowerShell 脚本。 建议仅将本指南用于试验环境，因为其中很多步骤将发生于现实生活中的生产过程。 可以使用这些证书通过 X.509 证书身份验证在 Azure IoT 中心模拟安全性。 本指南中的步骤在 Windows 计算机上本地创建证书。 

## <a name="prerequisites"></a>先决条件
本教程假定你已获取 OpenSSL 二进制文件。 你还可
    - 下载 OpenSSL 源代码并在计算机上生成二进制文件，或者 
    - 下载并安装任何[第三方 OpenSSL 二进制文件](https://wiki.openssl.org/index.php/Binaries)，例如从 [SourceForge 上的此项目](https://sourceforge.net/projects/openssl/)下载并安装。

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>创建 X.509 证书
以下步骤显示如何在本地创建 X.509 根证书的示例。 

1. 以管理员身份打开 PowerShell 窗口。  
   **注意：** 必须在 PowerShell 本身（而不是 PowerShell ISE、Visual Studio Code 或包装基础 PowerShell 控制台的其他工具）中打开此窗口。  使用基于非控制台的 PowerShell 将导致下面的 `openssl` 命令挂起。

2. 导航到工作目录。 运行以下脚本以设置全局变量。 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertCommonName      = "Azure IoT Root CA"
    $_rootCertSubject         = "CN=$_rootCertCommonName"
    $_intermediateCertSubject = "Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. 运行以下脚本，将 OpenSSL 二进制文件复制到工作目录并设置环境变量：

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. 接下来运行以下脚本，搜索是否已安装指定的使用者名称的证书，以及是否在计算机上正确配置 OpenSSL：
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    正确配置所有内容后，将显示“成功”消息。 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>创建 X.509 证书链
通过运行以下 PowerShell 脚本创建包含根 CA 的证书链，例如此示例使用的“CN=Azure IoT Root CA”。 此脚本还更新 Windows 操作系统证书存储，并在工作目录中创建证书文件。 
    1. 以下脚本创建 PowerShell 函数，为给定使用者名称和签名机构创建自签名证书。 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$commonName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$commonName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. 以下 PowerShell 函数使用前面的函数以及 OpenSSL 二进制文件创建 X.509 中间证书。 
    ```PowerShell
    function New-CAIntermediateCert([string]$commonName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($commonName + ".cer")
        $newCert = New-CASelfsignedCertificate $commonName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. 以下 PowerShell 函数创建 X.509 证书链。 阅读[证书链](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)，了解详细信息。
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    此脚本在工作目录创建名为 RootCA.cer 的文件。 
    4. 最后，通过在 PowerShell 窗口中运行命令 `New-CACertChain`，使用上述 PowerShell 函数创建 X.509 证书链。 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>X.509 CA 证书的所有权证明

此脚本执行 X.509 证书的所有权证明流程。 

在桌面上的 PowerShell 窗口中运行以下代码：
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $requestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $requestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

此代码将以名为 VerifyCert4.cer 的文件在工作目录中创建具有给定使用者名称且由 CA 签名的证书。 此证书文件有助于向 IoT 中心验证你是否具有此 CA 的签名权限（即私钥）。


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>创建设备的 X.509 叶证书

本部分介绍可以使用 PowerShell 脚本创建叶设备证书及相应证书链。 

在本地计算机上的 PowerShell 窗口中，运行以下脚本创建此设备的 CA 签名的 X.509 证书：

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $deviceName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

然后使用用于创建设备的友好名称在 PowerShell 窗口中运行 `New-CADevice "<yourTestDevice>"`。 当系统提示输入 CA 私钥的密码时，请输入“123”。 这将在工作目录中创建 <yourTestDevice>.pfx 文件。

## <a name="clean-up-certificates"></a>清理证书

在开始栏或“设置”应用中，搜索并选择“管理计算机证书”。 删除 **Azure IoT CA TestOnly*** 颁发的任何证书。 这些证书应存在于以下三个位置中： 

* 证书 - 本地计算机 > 个人 > 证书
* 证书 - 本地计算机 > 受信任的根证书颁发机构 > 证书
* 证书 - 本地计算机 > 中间证书颁发机构 > 证书

   ![删除 Azure IoT CA TestOnly 证书](./media/iot-hub-security-x509-create-certificates/cleanup.png)
