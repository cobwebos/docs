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
ms.openlocfilehash: 115c6726ed150b9a76685bd5a558d547d215958f
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587729"
---
# <a name="configure-export-policy-for-a-volume"></a>为卷配置导出策略

你可以配置导出策略来控制对 Azure NetApp 文件卷的访问。 

## <a name="steps"></a>Steps 

1.  从“管理卷”边栏选项卡中单击“创建导出策略”边栏选项卡。 

2.  为以下字段指定信息来创建导出策略规则：   
    *  **索引**   
        指定规则的索引号。  
        导出策略最多可以包含五个规则。 规则将按照它们在索引号列表中的顺序进行评估。 首先将评估索引号较小的规则。 例如，索引号为 1 的规则将在索引号为 2 的规则前进行评估。 

    * **允许的客户端**   
        采用以下格式之一指定值：  
        * IPv4 地址，例如 `10.1.12.24` 
        * 带有以位数表示的子网掩码的 IPv4 地址，例如 `10.1.12.10/4`

    * **Access**  
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
