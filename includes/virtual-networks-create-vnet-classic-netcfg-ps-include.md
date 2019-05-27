---
title: include 文件
description: include 文件
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116832"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>如何通过 PowerShell 使用网络配置文件创建虚拟网络
Azure 使用 xml 文件定义适用于订阅的所有虚拟网络。 可以下载此文件并进行编辑，以修改或删除现有虚拟网络并创建新的虚拟网络。 通过本教程，可了解如何下载此文件（称为网络配置（或 netcgf）文件），并进行编辑，创建新的虚拟网络。 若要深入了解网络配置文件，请参阅 [Azure 虚拟网络配置架构](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

若要通过 PowerShell 使用 netcfg 文件创建虚拟网络，请完成以下步骤：

1. 如果从未用过 Azure PowerShell，请完成[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 一文中的步骤，然后登录 Azure 并选择订阅。
2. 从 Azure PowerShell 控制台中，通过运行以下命令使用 Get-AzureVnetConfig cmdlet 将网络配置文件下载到计算机上的某个目录下： 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   预期输出：
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. 打开在步骤 2： 使用任何 XML 或文本编辑器应用程序，保存的文件并查找 **\<VirtualNetworkSites >** 元素。 如果已创建网络，每个网络显示为其自身 **\<VirtualNetworkSite >** 元素。
4. 若要创建在此方案中所述的虚拟网络，请正下方添加以下 XML  **\<VirtualNetworkSites >** 元素：

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. 保存网络配置文件。
6. 从 Azure PowerShell 控制台中，通过运行以下命令使用 Set-AzureVnetConfig cmdlet 上传网络配置文件： 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   返回的输出：
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   如果 OperationStatus 在返回的输出中不是“成功”状态，请检查 xml 文件是否出现任何错误，并再次完成步骤 6 的操作。

7. 从 Azure PowerShell 控制台中，通过运行以下命令使用 Get-AzureVnetSite cmdlet 验证是否已添加新网络： 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   返回（缩写）的输出包含以下文本：
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
