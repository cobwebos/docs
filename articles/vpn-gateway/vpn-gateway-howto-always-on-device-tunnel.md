---
title: 为 VPN 网关配置 Always On VPN 隧道
description: 配置 VPN 网关 Always On VPN 隧道的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 98d8c2f6870be16f3eb92219fc3d02f988390a41
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295469"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>配置 Always On VPN 设备隧道

Windows 10 虚拟专用网络 (VPN) 客户端的一项新功能是能够维护 VPN 连接。 Always On 是一种 Windows 10 功能, 它使活动的 VPN 配置文件能够自动连接, 并根据触发器 (即用户登录、网络状态更改或设备屏幕活动) 保持连接状态。

可以将 Azure 虚拟网络网关与 Windows 10 Always On 结合使用, 以便建立持久用户隧道以及设备隧道到 Azure。 本文将帮助你配置 Always ON VPN 设备隧道。

Always On VPN 连接包括两种类型的隧道:

* **设备隧道**在用户登录设备之前连接到指定的 VPN 服务器。 预登录连接方案和设备管理目的使用设备隧道。

* **用户隧道**仅在用户登录设备后进行连接。 用户隧道允许用户通过 VPN 服务器访问组织资源。

设备隧道和用户隧道独立操作其 VPN 配置文件。 它们可以同时连接, 并且可以根据需要使用不同的身份验证方法和其他 VPN 配置设置。

## <a name="1-configure-the-gateway"></a>1.配置网关

将 VPN 网关配置为使用 IKEv2, 并使用此[点到站点项目](vpn-gateway-howto-point-to-site-resource-manager-portal.md)进行基于证书的身份验证。

## <a name="2-configure-the-user-tunnel"></a>2.配置用户隧道

1. 在 Windows 10 客户端上安装客户端证书, 如此[点到站点 VPN 客户端一文](point-to-site-how-to-vpn-client-install-azure-cert.md)中所示。 证书必须位于当前用户存储中
2. 使用[这些说明](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections), 通过 POWERSHELL、SCCM 或 Intune 配置 Always On VPN 客户端。

## <a name="3-configure-the-device-tunnel"></a>3.配置设备隧道

必须满足以下要求才能成功建立设备隧道:

* 设备必须是已加入域且运行 Windows 10 企业版或教育版1709或更高版本的计算机。
* 隧道仅适用于 Windows 内置 VPN 解决方案, 并且是使用 IKEv2 和计算机证书身份验证建立的。 
* 每台设备只能配置一个设备隧道。

1. 在 Windows 10 客户端上安装客户端证书, 如此[点到站点 VPN 客户端一文](point-to-site-how-to-vpn-client-install-azure-cert.md)中所示。 该证书必须位于本地计算机存储区中。
1. 使用[这些说明](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)创建 VPN 配置文件, 并在本地系统帐户的上下文中配置设备隧道。

### <a name="configuration-example-for-device-tunnel"></a>设备隧道的配置示例

配置虚拟网络网关并将客户端证书安装到 Windows 10 客户端上的本地计算机存储中后, 请使用以下示例来配置客户端设备隧道。

1. 复制以下文本并将其保存为***devicecert***。

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 复制以下文本, 并将其作为***VPNProfile***保存在与**devicecert**相同的文件夹中。 编辑以下文本以匹配您的环境。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. 从[Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec)下载**PsExec** , 并将文件解压缩到**C:\PSTools**。
1. 在管理员命令提示符下, 通过运行以下命令启动 PowerShell:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. 在 PowerShell 中, 切换到**devicecert**和**VPNProfile**所在的文件夹, 并运行以下命令:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. 运行**rasphone**。

   ![rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. 查找**MachineCertTest**条目, 并单击 "**连接**"。

   ![连接](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. 如果连接成功, 请重新启动计算机。 隧道会自动连接。

## <a name="cleanup"></a>清理

若要删除配置文件, 请运行以下命令:

![清理](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

有关疑难解答, 请参阅[Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
