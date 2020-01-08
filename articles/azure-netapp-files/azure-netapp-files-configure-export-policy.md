---
title: 配置 NFS 卷的导出策略-Azure NetApp 文件
description: 介绍如何使用 Azure NetApp 文件配置导出策略，以控制对 NFS 卷的访问
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551552"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>为 NFS 卷配置导出策略

你可以配置导出策略来控制对 Azure NetApp 文件卷的访问。 Azure NetApp 文件导出策略仅支持 NFS 卷。  支持 NFSv3 和 NFSv4。 

## <a name="steps"></a>步骤 

1.  在 Azure NetApp 文件导航窗格中单击 "**导出策略**"。 

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

    ![导出策略](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>后续步骤 
* [管理卷](azure-netapp-files-manage-volumes.md)
* [为虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照](azure-netapp-files-manage-snapshots.md)
