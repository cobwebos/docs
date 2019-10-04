---
title: 为 VPN 网关配置 Always On VPN 隧道
description: 配置 VPN 网关 Always On 用户 VPN 隧道的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846467"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>配置 Always On VPN 用户隧道

Windows 10 虚拟专用网络 (VPN) 客户端的一项新功能是维护 VPN 连接。 Always On 是一项 Windows 10 功能，它使用有效的 VPN 配置文件根据触发器（即，用户登录、网络状态更改或设备屏幕活动状态）自动建立连接并保持连接。

可将 Azure 虚拟网络网关与 Windows 10 Always On 配合使用，以便与 Azure 建立持久性用户隧道和设备隧道。 本文将帮助你配置 Always On VPN 用户隧道。

Always On VPN 连接包括两种隧道：

* **设备隧道**在用户登录设备之前连接到指定的 VPN 服务器。 预登录连接方案和设备管理将使用设备隧道。

* **用户隧道**只会在用户登录设备后进行连接。 用户隧道允许用户通过 VPN 服务器访问组织资源。

设备隧道和用户隧道使用其自身的 VPN 配置文件独立运行。 它们可以同时连接，在适当的情况下可以使用不同的身份验证方法和其他 VPN 配置设置。

## <a name="1-configure-the-gateway"></a>1.配置网关

使用此[点到站点文章](vpn-gateway-howto-point-to-site-resource-manager-portal.md)将 VPN 网关配置为使用 IKEv2 和基于证书的身份验证。

## <a name="2-configure-the-user-tunnel"></a>2.配置用户隧道

1. 如此[点到站点 VPN 客户端文章](point-to-site-how-to-vpn-client-install-azure-cert.md)中所述，在 Windows 10 客户端上安装客户端证书。 该证书需位于当前用户存储中
2. 使用[这些说明](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)通过 PowerShell、SCCM 或 Intune 配置 Always On VPN 客户端。

### <a name="configuration-example-for-user-tunnel"></a>用户隧道的配置示例

配置虚拟网络网关并在 Windows 10 客户端的本地计算机存储中安装客户端证书后，使用以下示例配置客户端设备隧道。

1. 复制以下文本并将其保存为***usercert***。

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
1. 复制以下文本，并将其作为***VPNProfile***保存在与**usercert**相同的文件夹中。 编辑以下文本，使之与你的环境相匹配。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. 以管理员身份运行 PowerShell。

1. 在 PowerShell 中，切换到**usercert**和**VPNProfile**所在的文件夹，并运行以下命令：

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. 在 "VPN 设置" 下查看。

1. 查找**UserTest**条目，并单击 "**连接**"。

1. 如果连接成功，则已成功配置 always on 用户隧道。

## <a name="cleanup"></a>清理

若要删除配置文件，请运行以下命令：

1. 断开连接并取消选中 "自动连接"

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![清理](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>后续步骤

若要进行故障排除，请参阅 [Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
