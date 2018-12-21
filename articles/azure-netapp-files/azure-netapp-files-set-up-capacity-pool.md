---
title: 为 Azure NetApp 文件设置容量池 |Microsoft Docs
description: 介绍了如何设置容量池，以便可以在其中创建卷。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 55a1d16ce1617ecf7bc28c7c62de8557ceeea311
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412905"
---
# <a name="set-up-a-capacity-pool"></a>设置容量池
设置容量池可以在其中创建卷。  

## <a name="before-you-begin"></a>开始之前 
必须已经创建了一个 NetApp 帐户。   

[创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Steps 

1. 转到你的 NetApp 帐户的管理边栏选项卡，从导航窗格中选择“容量池”。

2. 单击“+ 添加池”以创建一个新容量池。   
    此时将显示“新建容量池”窗口。

3. 提供新容量池的以下信息：  
  * **名称**  
    指定容量池的名称。  
    容量池名称对于每个 NetApp 帐户必须是唯一的。

  * **服务级别**   
    此字段显示容量池的目标性能。  
    目前，仅提供了高级服务级别。 

  *  **大小**     
      指定你购买的容量池的大小。        
      最小容量池大小是 4 TiB。 可以创建大小为 4 TiB 的倍数的池。   
      
      ![新建容量池](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. 单击“确定”。

## <a name="next-steps"></a>后续步骤 

[将子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)


