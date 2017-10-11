---
title: "配置 Azure 虚拟网络（经典）- 网络配置文件 | Microsoft Docs"
description: "了解如何通过导出、更改和导入网络配置文件来创建和修改虚拟网络（经典）。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f1e3ae26b6525f2235a6b0d53546b334dc027b94
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>使用网络配置文件配置虚拟网络（经典）
> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 部署模型。

可以在 Azure 命令行接口 (CLI) 1.0 或 Azure PowerShell 中使用使用网络配置文件来创建和配置虚拟网络（经典）。 无法通过 Azure Resource Manager 部署模型使用网络配置文件创建或修改虚拟网络。 无法在 Azure 门户中使用网络配置文件来创建或修改虚拟网络（经典），但可以直接在 Azure 门户中创建虚拟网络（经典），而无需使用网络配置文件。

使用网络配置文件创建和配置虚拟网络（经典）需要导出、更改和导入该文件。

## <a name="export"></a>导出网络配置文件

可以使用 PowerShell 或 Azure CLI 导出网络配置文件。 PowerShell 导出 XML 文件，而 Azure CLI 导出 json 文件。

### <a name="powershell"></a>PowerShell
 
1. [安装 Azure PowerShell 并登录到 Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 在以下命令中根据需要更改目录（并确保它存在）和文件名，然后运行该命令导出网络配置文件：

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [安装 Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 通过 Azure CLI 1.0 命令提示符完成剩余的步骤。
2. 输入 `azure login` 命令登录到 Azure。
3. 输入 `azure config mode asm` 命令确保处于 asm 模式。
4. 在以下命令中根据需要更改目录（并确保它存在）和文件名，然后运行该命令导出网络配置文件：
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>创建或修改网络配置文件

网络配置文件是一个 XML 文件（使用 PowerShell 时）或一个 json 文件（使用 Azure CLI 时）。 可在任何文本或 XML/json 编辑器中编辑该文件。 [网络配置文件架构设置](https://msdn.microsoft.com/library/azure/jj157100.aspx)一文中提供了所有设置的详细信息。 有关设置的更多说明，请参阅[查看虚拟网络和设置](virtual-network-manage-network.md#view-vnet)。 对文件所做的更改：

- 必须符合架构，否则导入网络配置文件将会失败。
- 覆盖订阅的所有现有网络设置，因此，在修改时请格外小心。 例如，请参考下面的示例网络配置文件。 假设原始文件包含两个 **VirtualNetworkSite** 实例，而你如示例中所示更改了此文件。 导入文件时，Azure 会删除你在该文件中删除的 **VirtualNetworkSite** 的虚拟网络实例。 这种简化方案假设虚拟网络中没有任何资源，如果有，则无法删除虚拟网络，并且导入将会失败。

> [!IMPORTANT]
> Azure 会将部署有项目的子网视为“**使用中**”。 当某个子网处于“使用中”状态时，不能对其进行修改。 在修改网络配置文件中的子网信息之前，请将已部署到子网的任何内容移到不会进行修改的其他子网。 有关详细信息，请参阅[将 VM 或角色实例移到其他子网](virtual-networks-move-vm-role-to-subnet.md)。

### <a name="example-xml-for-use-with-powershell"></a>用于 PowerShell 的示例 XML

以下示例网络配置文件在“美国东部”Azure 区域创建名为 *myVirtualNetwork*、地址空间为 *10.0.0.0/16* 的虚拟网络。 该虚拟网络包含一个名为 *mySubnet*、地址前缀为 *10.0.0.0/24* 的子网。

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

如果导出的网络配置文件不包含任何内容，则你可以复制上述示例中的 XML，并将其粘贴到新文件中。

### <a name="example-json-for-use-with-the-azure-cli-10"></a>用于 Azure CLI 1.0 的示例 JSON

以下示例网络配置文件在“美国东部”Azure 区域创建名为 *myVirtualNetwork*、地址空间为 *10.0.0.0/16* 的虚拟网络。 该虚拟网络包含一个名为 *mySubnet*、地址前缀为 *10.0.0.0/24* 的子网。

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

如果导出的网络配置文件不包含任何内容，则你可以复制上述示例中的 json，并将其粘贴到新文件中。

## <a name="import"></a>导入网络配置文件

可以使用 PowerShell 或 Azure CLI 导入网络配置文件。 PowerShell 导入 XML 文件，而 Azure CLI 导入 json 文件。 如果导入失败，请确认该文件是否符合[网络配置架构](https://msdn.microsoft.com/library/azure/jj157100.aspx)。 

### <a name="powershell"></a>PowerShell
 
1. [安装 Azure PowerShell 并登录到 Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 在以下命令中根据需要更改目录和文件名，然后运行该命令导入网络配置文件：
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [安装 Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 通过 Azure CLI 1.0 命令提示符完成剩余的步骤。
2. 输入 `azure login` 命令登录到 Azure。
3. 输入 `azure config mode asm` 命令确保处于 asm 模式。
4. 在以下命令中根据需要更改目录和文件名，然后运行该命令导入网络配置文件：

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
