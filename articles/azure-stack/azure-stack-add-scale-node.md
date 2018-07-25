---
title: Azure Stack 添加缩放节点 |Microsoft Docs
description: 将节点添加到 Azure Stack 中的缩放单位。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: thoroet
ms.openlocfilehash: 15c46bb0fee4689b614636d8c9e01e36f7f0be3d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228233"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>在 Azure Stack 中添加其他缩放单位节点

Azure Stack 操作员可以通过添加更多的物理计算机增加总容量的现有的缩放单位。 物理计算机也称为缩放单位节点。 您将添加每个新的缩放单位节点必须是同构中 CPU 类型、 内存和磁盘数和大小的节点的缩放单位中已存在。  

> [!NOTE]  
您必须运行 Azure Stack 1807 或更高版本将添加其他缩放单位节点。

若要添加的缩放单位节点，你在 Azure Stack 采取行动，并运行工具从您的硬件设备制造商 (OEM)。 在硬件生命周期主机 (HLH) 以确保新的物理计算机与现有节点相同的固件级别上运行的 OEM 工具。

以下流程图显示添加的缩放单位节点的一般过程。

![添加规模单位流](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *OEM 硬件供应商是否会制定物理服务器机架放置和更新固件变化根据支持合同。*

要添加新节点的操作可能需要几个小时或数天才能完成。

## <a name="add-scale-unit-nodes"></a>添加缩放单位节点 
以下步骤，如何将节点添加的高级概述。 不，第一个参考 OEM 提供容量扩展文档的情况下，请执行以下步骤。

1. 新的物理服务器机架放在正确布线。 
2. 基板管理控制器 (BMC) 中配置正确的 IP 地址并将应用所有的 BIOS 设置每个 OEM 提供的文档。
3. 使用 HLH 上运行的硬件制造商提供的工具将当前固件基线应用于所有组件。
4. 运行 Azure Stack 管理员门户中添加节点操作。
5. 验证添加节点操作成功。 若要执行此操作，请检查[**状态**的缩放单位](#monitor-add-node-operations)。 

## <a name="add-the-node"></a>添加节点 
可以使用管理控制台或 PowerShell 来添加新节点。 添加节点操作首先将新的缩放单位节点添加为可用的计算容量，然后自动将扩展的存储容量。 由于 Azure Stack 是超聚合系统自动扩展容量其中*计算*并*存储*进行整体缩放。

### <a name="use-the-admin-console"></a>使用管理控制台
1. 以 Azure Stack 操作员身份登录到 Azure Stack 管理员门户。
2. 导航到**区域管理** > **缩放单位**，然后选择你想要扩展容量，以便的缩放单位。  
   ![选择缩放单位](media/azure-stack-add-scale-node/select-node1.png)

   ![查看缩放单位的详细信息](media/azure-stack-add-scale-node/select-node2.png)
 
3. 以下参数是必需的：  
   - **BMC IP 地址**为您添加每个缩放单位节点。 （每行的一个 IP 地址。）![添加节点](media/azure-stack-add-scale-node/add-node.png)

### <a name="use-powershell"></a>使用 PowerShell

使用**新建 AzsScaleUnitNodeObject** cmdlet 将添加一个或多个节点。  

使用下面的示例 PowerShell 脚本的任何一个之前, 的值替换为*节点名称*并*IP 地址*与 Azure Stack 环境中的值。

**为单个节点：**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

**添加多个节点：**
  ```powershell
  ## Add multiple nodes 
  $NewNode1=New-AzsScaleUnitNodeObject -computername "<name_of_new_node01>"  -BMCIPv4Address "<BMCIP_address_of_new_node01>" 
 
  $NewNode2=New-AzsScaleUnitNodeObject -computername "<name_of_new_node02>" -BMCIPv4Address “<BMCIP_address_of_new_node02>”$ 
 
  Add-AzsScaleUnitNode -NodeList @($NewNode1,$NewNode2) -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```

## <a name="monitor-add-node-operations"></a>监视添加节点操作 
可以使用管理门户或 PowerShell 来获取添加节点操作的状态。 添加节点操作可能需要数天才能完成几个小时。

### <a name="use-the-admin-console"></a>使用管理控制台 
若要监视添加新节点，在管理门户中查看缩放单位，或缩放单位节点对象。 若要执行此操作，请转到**区域管理** > **缩放单位**。 接下来，选择您想要查看缩放单位节点的缩放单位。 

### <a name="use-powershell"></a>使用 PowerShell
可以按如下所示使用 PowerShell 检索缩放单位和缩放单位节点的状态：
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>添加节点操作的状态 
**缩放单位：**
|状态               |说明  |
|---------------------|---------|
|正在运行              |所有节点积极都参与的缩放单位。|
|已停止              |缩放单位节点已关闭或不可访问。|
|扩展            |一个或多个缩放单位节点当前正在添加为计算容量。|
|配置存储  |已扩展的计算容量和存储配置正在运行。|
|需要修正 |检测到需要在要修复的一个或多个缩放单位节点的错误。|


**有关缩放单位节点：**
|状态                |说明  |
|----------------------|---------|
|正在运行               |节点方正在参与的缩放单位。|
|已停止               |该节点是不可用。|
|正在添加                |主动正在添加到缩放单位节点。|
|正在修复             |正在修复节点。|
|维护           |该节点将暂停，并且没有任何活动的用户工作负荷运行。 |
|需要修正  |检测到需要的节点要修复错误。|


## <a name="troubleshooting"></a>故障排除
以下是所示添加节点时的常见问题。 

**方案 1:** 添加缩放单位节点操作将失败，但一个或多个节点列出且状态为已停止。  
- 修正： 使用修复操作修复一个或多个节点。 仅在单个修复操作可以运行一次。

**方案 2:** 已添加一个或多个缩放单位节点，但存储扩展失败。 在此方案中，缩放单位节点对象报告的运行状态，但未启动配置的存储任务。  
- 修正： 使用特权终结点来查看存储运行状况，通过运行以下 PowerShell cmdlet:
 
**方案 3:** 收到警报，指示存储向外缩放作业失败。  
- 补救措施： 在这种情况下，存储配置任务失败。 此问题，需要联系支持人员。


## <a name="next-steps"></a>后续步骤 
查看[节点操作](azure-stack-node-actions.md) 
