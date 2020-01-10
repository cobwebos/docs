---
title: 配置 Always On VPN 用户隧道
titleSuffix: Azure VPN Gateway
description: 本文介绍如何为 VPN 网关配置 Always On VPN 用户隧道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bff2ed48a78bfbae984dea5e5474971817023bc6
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729315"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>配置 Always On VPN 用户隧道

Windows 10 VPN 客户端 Always On 的一项新功能是能够维护 VPN 连接。 对于 Always On，活动的 VPN 配置文件可以自动连接，并根据触发器（例如用户登录、网络状态更改或设备屏幕活动）保持连接。

可以通过 Windows 10 Always On 使用 Azure 虚拟网络网关，建立到 Azure 的持久用户隧道和设备隧道。 本文可帮助你配置 Always On VPN 用户隧道。

Always On VPN 连接包括两种类型的隧道：

* **设备隧道**：在用户登录到设备之前连接到指定的 VPN 服务器。 预登录连接方案和设备管理使用设备隧道。

* **用户隧道**：仅在用户登录到设备后进行连接。 通过使用用户隧道，你可以通过 VPN 服务器访问组织资源。

设备隧道和用户隧道独立于其 VPN 配置文件运行。 它们可以同时连接，并且可以根据需要使用不同的身份验证方法和其他 VPN 配置设置。

以下部分介绍了如何配置 VPN 网关和用户隧道。

## <a name="step-1-configure-a-vpn-gateway"></a>步骤1：配置 VPN 网关

按照此[点到站点一](vpn-gateway-howto-point-to-site-resource-manager-portal.md)文中的说明，将 VPN 网关配置为使用 IKEv2 和基于证书的身份验证。

## <a name="step-2-configure-a-user-tunnel"></a>步骤2：配置用户隧道

1. 在 Windows 10 客户端上安装客户端证书，如此[点到站点 VPN 客户端一](point-to-site-how-to-vpn-client-install-azure-cert.md)文中所示。 证书必须位于当前用户存储区中。

1. 按照[配置 Windows 10 客户端 ALWAYS ON vpn 连接](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)中的说明，通过 PowerShell、System Center Configuration Manager 或 Intune 配置 Always On VPN 客户端。

### <a name="example-configuration-for-the-user-tunnel"></a>用户隧道的示例配置

配置虚拟网络网关并在 Windows 10 客户端上的本地计算机存储中安装客户端证书后，请使用以下示例配置客户端设备隧道：

1. 复制以下文本，并将其另存为*usercert*：

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
1. 复制以下文本，并将其作为*VPNProfile*保存在与*usercert*相同的文件夹中。 编辑以下文本以匹配你的环境：

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

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

1. 在 PowerShell 中，切换到*usercert*和*VPNProfile*所在的文件夹，并运行以下命令：

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. 在 " **VPN 设置**" 下，查找 " **UserTest** " 条目，然后选择 "**连接**"。

1. 如果连接成功，则已成功配置 Always On 用户隧道。

## <a name="clean-up-your-resources"></a>清理资源

若要删除配置文件，请执行以下操作：

1. 运行以下命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 断开连接，并清除 "**自动连接**" 复选框。

![清理](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>后续步骤

若要解决可能出现的任何连接问题，请参阅[Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
