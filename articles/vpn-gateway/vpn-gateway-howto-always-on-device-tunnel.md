---
title: 为 VPN 网关配置 Always On VPN 隧道
description: 为 VPN 网关配置 Always On VPN 隧道的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695842"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>配置 Always On VPN 设备隧道

Windows 10 虚拟专用网络 (VPN) 客户端的新功能之一是能够维护 VPN 连接。 Always On 是一种使活动 VPN 配置文件来自动连接并保持连接基于触发器的 Windows 10 功能 — 即，用户登录、 网络状态更改或设备屏幕活动。

Azure 虚拟网络网关可以使用与 Windows 10 Always On 来建立永久性用户隧道以及到 Azure 设备隧道。 本文将帮助你配置 Always ON VPN 设备隧道。

Always On VPN 连接包括两种类型的隧道：

* **设备隧道**连接到指定的 VPN 服务器之前用户登录设备。 预登录连接方案进行设备管理使用设备隧道。

* **用户隧道**连接仅在用户登录设备后。 用户隧道允许用户通过 VPN 服务器访问组织资源。

使用自己的 VPN 配置文件设备隧道和用户隧道独立运行。 它们可以一次连接，并可以根据需要使用不同的身份验证方法和其他 VPN 配置设置。

## <a name="1-configure-the-gateway"></a>1.配置网关

配置 VPN 网关以使用 IKEv2 和使用此基于证书的身份验证[点到站点文章](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。

## <a name="2-configure-the-user-tunnel"></a>2.配置用户隧道

1. 在 Windows 10 客户端上安装客户端证书，如在此所述[点到站点 VPN 客户端文章](point-to-site-how-to-vpn-client-install-azure-cert.md)。 该证书必须是当前用户存储中
2. 配置 Always On VPN 客户端通过使用 PowerShell、 SCCM 或 Intune[这些说明](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)。

## <a name="3-configure-the-device-tunnel"></a>3.配置设备隧道

为了成功建立设备隧道，必须满足以下要求：

* 设备必须加入的域的计算机运行 Windows 10 企业版或教育版本 1709年或更高版本。
* 隧道仅可配置 Windows 内置 VPN 解决方案并且使用 IKEv2 计算机证书身份验证建立。 
* 只有一个设备隧道可以配置每个设备。

1. 在 Windows 10 客户端上安装客户端证书，如在此所述[点到站点 VPN 客户端文章](point-to-site-how-to-vpn-client-install-azure-cert.md)。 该证书必须是本地计算机存储中。
1. 使用[这些说明](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)创建 VPN 配置文件和配置设备隧道的本地系统帐户上下文中。

### <a name="configuration-example-for-device-tunnel"></a>设备隧道的配置示例

配置虚拟网络网关并安装 Windows 10 客户端上的本地计算机存储中的客户端证书后，使用下面的示例配置客户端设备隧道。

1. 复制以下文本并将其保存为***devicecert.ps1***。

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
1. 复制以下文本并将其保存为***VPNProfile.xml***所在的同一文件夹中**devicecert.ps1**。 编辑以下文本以匹配你的环境。

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
1. 下载**PsExec**从[Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec)并将文件解压缩到**C:\PSTools**。
1. 从管理 CMD 提示符，通过运行启动 PowerShell:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. 在 PowerShell 中，切换到的文件夹位置**devicecert.ps1**并**VPNProfile.xml**所在，并运行以下命令：

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. 运行**rasphone 将**。

   ![rasphone 将](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. 寻找**MachineCertTest**条目，单击**Connect**。

   ![连接](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. 如果连接成功，重新启动计算机。 隧道将自动连接。

## <a name="cleanup"></a>清理

若要删除该配置文件，请运行以下命令：

![清理](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>后续步骤

有关故障排除，请参阅[Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
