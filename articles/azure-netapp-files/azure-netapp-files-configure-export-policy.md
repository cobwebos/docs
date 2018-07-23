---
title: 为 Azure NetApp 文件卷配置导出策略 |Microsoft Docs
description: 介绍了如何配置导出策略来控制对 Azure NetApp 文件卷的访问
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
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010950"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>为卷配置导出策略（可选）

你可以配置导出策略来控制对 Azure NetApp 文件卷的访问。 

## <a name="steps"></a>步骤 

1.  从“管理卷”边栏选项卡中单击“创建导出策略”边栏选项卡。 

2.  为以下字段指定信息来创建导出策略规则：   
    *  **索引**   
        指定规则的索引号。  
        导出策略最多可以包含五个规则。 规则将按照它们在索引号列表中的顺序进行评估。 首先将评估索引号较小的规则。 例如，索引号为 1 的规则将在索引号为 2 的规则前进行评估。 

    * **允许的客户端**   
        采用以下格式之一指定值：  
        * IPv4 地址，例如 `10.1.12.24` 
        * 带有以位数表示的子网掩码的 IPv4 地址，例如 `10.1.12.10/4`

    * **访问权限**  
        选择以下访问权限类型之一：  
        * 无访问权限 
        * 读取和写入
        * 只读

    * **协议**   
        指定要用于导出策略的协议。   
        目前，Azure NetApp 文件仅支持 NFSv3。

    ![导出策略](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>后续步骤 
* [管理卷](azure-netapp-files-manage-volumes.md)
* [为虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照](azure-netapp-files-manage-snapshots.md)
