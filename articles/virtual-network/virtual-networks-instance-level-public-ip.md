---
title: "使用 PowerShell 的实例级公共 IP（经典）|Microsoft 文档"
description: "了解 ILPIP (PIP) 以及如何使用 PowerShell 对其进行管理。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f1919d84cf912e184d87a5eeb462355e8ee3da07


---
# <a name="instance-level-public-ip-classic-overview"></a>实例级公共 IP（经典）概述
实例级公共 IP (ILPIP) 是可直接向 VM 或角色实例（而非 VM 或角色实例所在的云服务）分配的公共 IP 地址。 这不会取代分配给你的云服务的 VIP（虚拟 IP）。 而是可以用来直接连接到 VM 或角色实例的其他 IP 地址。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器。 请确保你了解 [IP 地址](virtual-network-ip-addresses-overview-classic.md)在 Azure 中的工作原理。

![ILPIP 和 VIP 之间的差异](./media/virtual-networks-instance-level-public-ip/Figure1.png)

如图 1 所示，云服务是使用 VIP 访问的，而各个 VM 通常是使用 VIP:&lt;端口号&gt;访问的。 通过将 ILPIP 分配给特定 VM，可以直接使用该 IP 地址访问该 VM。

当你在 Azure 中创建云服务时，系统会自动创建相应的 DNS A 记录，以便通过完全限定的域名 (FQDN) 而非实际 VIP 来访问服务。 系统会针对 ILPIP 执行相同的进程，以便通过 FQDN 而非 ILPIP 来访问 VM 或角色实例。 例如，如果你创建了名为 *contosoadservice* 的云服务，并且通过两个实例配置了名为 *contosoweb* 的 Web 角色，则 Azure 会为实例注册以下 A 记录：

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> 只能为每个 VM 或角色实例分配一个 ILPIP。 每个订阅最多可使用 5 个 ILPIP。 多 NIC VM 目前不支持 ILPIP。
> 
> 

## <a name="why-should-i-request-an-ilpip"></a>为什么应请求 ILPIP？
如果你想要能够通过直接向其分配的 IP 地址链接到 VM 或角色实例，请为 VM 或角色实例请求 ILPIP，而不是使用云服务VIP:&lt;端口号&gt;。

* **被动 FTP** - 如果 VM 上有 ILPIP，你可以接收几乎所有端口上的流量，而无需打开终结点来接收流量。 这样就可以启用与被动 FTP 类似的方案，以便动态选择端口。
* **出站 IP** - 源自 VM 的出站流量与充当源的 ILPIP 一起出站，这样就可以针对外部实体来唯一地标识 VM。

> [!NOTE]
> 过去，ILPIP 称为 PIP，表示公共 IP。
> 

## <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>在 VM 创建期间如何使用 PowerShell 请求 ILPIP
下面的 PowerShell 脚本将创建名为 *FTPService* 的全新云服务，然后从 Azure 中检索映像，并使用检索到的映像创建名为 *FTPInstance* 的 VM，接着将 VM 设置为使用 ILPIP，最后再将 VM 添加到新服务：

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>如何检索 VM 的 ILPIP 信息
若要查看使用以上脚本创建的 VM 的 ILPIP 信息，请运行以下 PowerShell 命令，然后观察 *PublicIPAddress* 和 *PublicIPName* 的值：

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

预期输出：
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>如何删除 VM 的 ILPIP
若要删除在以上脚本中添加到 VM 的 ILPIP，请运行以下 PowerShell 命令：

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>如何向现有 VM 添加 ILPIP
若要向使用以上脚本创建的 VM 添加 ILPIP，请运行以下命令：

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>如何使用服务配置文件将 ILPIP 关联到 VM
你可以使用服务配置 (CSCFG) 文件将 ILPIP 关联到 VM。 下面的示例 xml 显示了如何将云服务配置为将名为 *MyPublicIP* 的 ILPIP 用于角色实例： 

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>后续步骤
* 了解 [IP 寻址](virtual-network-ip-addresses-overview-classic.md)在经典部署模型中的工作原理。
* 了解[保留 IP](virtual-networks-reserved-public-ip.md)。




<!--HONumber=Jan17_HO1-->


