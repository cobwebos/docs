---
title: 排查不支持 TLS 1.2 的应用程序导致的问题 |Microsoft Docs
description: 排查不支持 TLS 1.2 的应用程序导致的问题
services: cloud-services
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: 85fb87e23fa11781587572e836d1439dd813170e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425028"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>排除不支持 TLS 1.2 的应用程序的故障
本文介绍如何在 Windows Server 2019 云服务 web 角色和辅助角色上启用较旧的 TLS 协议（TLS 1.0 和1.1），并应用旧密码套件以支持其他协议。 

我们了解到，虽然我们在执行弃用 TLS 1.0 和 TLS 1.1 的步骤，但我们的客户可能需要支持较旧的协议和密码套件，直到它们可以规划其弃用。  尽管我们不建议重新启用这些旧值，但我们将提供帮助客户的指导。 我们鼓励客户在实现本文中所述的更改之前评估回归风险。 

> [!NOTE]
> 来宾操作系统系列6版本通过禁用 1.0/1.1 密码来强制执行 TLS 1.2。 

  
## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>删除 TLS 1.0、TLS 1.1 和旧密码套件支持 
为了支持我们对使用最佳类加密的承诺，Microsoft 宣布了从1.0 年6月开始迁移到和 1.1 2017 的计划。   自从此次初始公告后，Microsoft 宣布在第一半2020版本的 Microsoft Edge 和 Internet Explorer 11 中默认禁用传输层安全性（TLS）1.0 和1.1。  Apple、Google 和 Mozilla 中的类似公告表明了行业的发展方向。   

## <a name="tls-configuration"></a>TLS 配置  
Windows Server 2019 cloud Server 映像配置了 TLS 1.0，在注册表级别禁用了 TLS 1.1。 这意味着部署到此版本 Windows 并使用 Windows stack 进行 TLS 协商的应用程序将不允许 TLS 1.0 和 TLS 1.1 通信。   

服务器还附带一组有限的密码套件： 

```Powershell
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```
 
## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>步骤1：创建 PowerShell 脚本以启用 TLS 1.0 和 TLS 1。1 

使用以下代码作为示例来创建启用较旧的协议和密码套件的脚本。   出于本文档的目的，此脚本将命名为： TLSsettings。 
  
```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 

Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  

    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  

    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 

    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  

    $restart  
}  

#*************************************************************************************************************** 

#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 

function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384" 

# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256" 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256" 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA" 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 

  return $cipherorder 
} 

  
#*************************************************************************************************************** 


#********************************************** REGISTRY KEYS **************************************************** 

  
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 

#*************************************************************************************************************** 

$localRegistryPath = @() 

# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 

#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 

# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) {  
   Write-Log -Message "Checking for existing of key: $($localRegistryPath [$i]) " -Logfile $logLocation  -Severity Information 
   If (!(Test-Path -Path $localRegistryPath [$i])) {  
        New-Item $localRegistryPath [$i] | Out-Null 
               Write-Log -Message "Creating key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information 
           } 
}  

#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 

For ($i = 0; $i -lt $localRegistryPath .Length; $i = $i + 1) { 
    if ($localRegistryPath [$i].Contains("Client") -Or $localRegistryPath [$i].Contains("Server")) { 
        Write-Log -Message "Enabling this key: $($localRegistryPath [$i]) "  -Logfile $logLocation -Severity Information  
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath [$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 

$cipherlist = @() 

# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$cipherorder = [System.String]::Join(",", $cipherlist) 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  

if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Log -Message "Creating key: $($CipherSuiteRegKey) "  -Logfile $logLocation -Severity Information 
}  

Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  

#********************************************* REBOOT ******************************************* 

Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4  
```
  
## <a name="step-2-create-a-command-file"></a>步骤2：创建命令文件 

使用以下信息创建 CMD 文件。 

```
IF "%ComputeEmulatorRunning%" == "true" ( 
   ECHO Not launching the script, since is DEV Machine >> "Log.txt" 2>&1 
) ELSE ( 

PowerShell .\TLSsettings.ps1   
  
) 

REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 

EXIT /B 0   
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>步骤3：将启动任务添加到角色的服务定义（未处理） 

下面是一个示例，显示辅助角色和 web 角色。 
  
```
<?xml version="1.0" encoding="utf-8"?> 
<ServiceDefinition name="NugetExampleCloudServices" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
  <WebRole name="WebRole1" vmsize="Standard_D1_v2"> 
    <Sites> 
      <Site name="Web"> 
        <Bindings> 
          <Binding name="Endpoint1" endpointName="Endpoint1" /> 
        </Bindings> 
      </Site> 
    </Sites> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
    <Endpoints> 
      <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
    </Endpoints> 
  </WebRole> 
  <WorkerRole name="WorkerRole1" vmsize="Standard_D1_v2"> 
    <Startup> 
      <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
      </Task> 
    </Startup> 
  </WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-4-validation"></a>步骤4：验证 

可以使用[SSLLabs](https://www.ssllabs.com/)来验证终结点的 TLS 状态 

 
