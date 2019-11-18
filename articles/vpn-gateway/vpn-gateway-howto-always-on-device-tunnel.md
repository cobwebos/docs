---
title: 配置始终启用的 VPN 隧道
titleSuffix: Azure VPN Gateway
description: 为 VPN 网关配置 Always On VPN 隧道的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: f22b29cfcaf1d4c4ce28b2b0557d70b281b6891f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146397"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>配置 Always On VPN 设备隧道

Windows 10 虚拟专用网络 (VPN) 客户端的一项新功能是维护 VPN 连接。 Always On 是一项 Windows 10 功能，它使用有效的 VPN 配置文件根据触发器（即，用户登录、网络状态更改或设备屏幕活动状态）自动建立连接并保持连接。

可将 Azure 虚拟网络网关与 Windows 10 Always On 配合使用，以便与 Azure 建立持久性用户隧道和设备隧道。 本文将帮助你配置 Always ON VPN 设备隧道。

Always On VPN 连接包括两种隧道：

* **设备隧道**在用户登录设备之前连接到指定的 VPN 服务器。 预登录连接方案和设备管理将使用设备隧道。

* **用户隧道**只会在用户登录设备后进行连接。 用户隧道允许用户通过 VPN 服务器访问组织资源。

设备隧道和用户隧道使用其自身的 VPN 配置文件独立运行。 它们可以同时连接，在适当的情况下可以使用不同的身份验证方法和其他 VPN 配置设置。

## <a name="1-configure-the-gateway"></a>1. 配置网关

使用此[点到站点文章](vpn-gateway-howto-point-to-site-resource-manager-portal.md)将 VPN 网关配置为使用 IKEv2 和基于证书的身份验证。

## <a name="2-configure-the-device-tunnel"></a>2. 配置设备隧道

必须满足以下要求才能成功建立设备隧道：

* 该设备必须是已加入域且运行 Windows 10 企业版或教育版 1709 或更高版本的计算机。
* 只可针对 Windows 内置 VPN 解决方案配置该隧道；结合计算机证书身份验证使用 IKEv2 建立该隧道。 
* 对于每台设备，只能配置一个设备隧道。

1. 如此[点到站点 VPN 客户端文章](point-to-site-how-to-vpn-client-install-azure-cert.md)中所述，在 Windows 10 客户端上安装客户端证书。 证书需位于本地计算机存储中。
1. 使用[这些说明](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)创建 VPN 配置文件，并在 LOCAL SYSTEM 帐户的上下文中配置设备隧道。

### <a name="configuration-example-for-device-tunnel"></a>设备隧道的配置示例

配置虚拟网络网关并在 Windows 10 客户端的本地计算机存储中安装客户端证书后，使用以下示例配置客户端设备隧道。

1. 复制以下文本，并将文件保存为 ***devicecert.ps1***。

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
1. 复制以下文本，并在 ***devicecert.ps1*** 所在的同一文件夹中将文件保存为 **VPNProfile.xml**。 编辑以下文本，使之与你的环境相匹配。

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
1. 从 **Sysinternals** 下载 [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)，并将文件解压缩到 **C:\PSTools**。
1. 在管理员命令提示符下，运行以下命令启动 PowerShell：

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. 在 PowerShell 中，切换到 **devicecert.ps1** 和 **VPNProfile.xml** 所在的文件夹，然后运行以下命令：

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. 运行 **rasphone**。

   ![rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. 找到 **MachineCertTest** 条目并单击“连接”。

   ![连接](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. 如果连接成功，请重新启动计算机。 隧道将自动连接。

## <a name="cleanup"></a>清理

若要删除配置文件，请运行以下命令：

![清理](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

若要进行故障排除，请参阅 [Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
