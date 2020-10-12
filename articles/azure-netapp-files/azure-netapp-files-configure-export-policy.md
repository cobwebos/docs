---
title: 配置 NFS 卷的导出策略-Azure NetApp 文件
description: 介绍如何使用 Azure NetApp 文件配置导出策略，以控制对 NFS 卷的访问
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533186"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>为 NFS 卷配置导出策略

可以配置导出策略来控制对 Azure NetApp 文件卷的访问。 Azure NetApp 文件导出策略支持使用 NFS 协议的卷 (NFSv3 和 NFSv 4.1) 以及双重协议 (NFSv3 和 SMB) 。 

最多可以创建五个导出策略规则。

## <a name="steps"></a>步骤 

1.  从 "卷" 页中，选择要为其配置导出策略的卷，然后单击 " **导出策略**"。 

    你还可以在卷的创建过程中配置导出策略。

2.  为以下字段指定信息来创建导出策略规则：   
    *  **编入**   
        指定规则的索引号。  
        导出策略最多可以包含五个规则。 规则将按照它们在索引号列表中的顺序进行评估。 首先将评估索引号较小的规则。 例如，索引号为 1 的规则将在索引号为 2 的规则前进行评估。 

    * **允许的客户端**   
        采用以下格式之一指定值：  
        * IPv4 地址，例如 `10.1.12.24` 
        * 带有以位数表示的子网掩码的 IPv4 地址，例如 `10.1.12.10/4`

    * **访问**  
        选择以下访问权限类型之一：  
        * 无访问权限 
        * 读取和写入
        * 只读

    * **只读** 和 **读/写**  
        如果将 Kerberos 加密用于 NFSv 4.1，请按照 [配置 nfsv 4.1 Kerberos 加密](configure-kerberos-encryption.md)中的说明进行操作。  对于 Kerberos 的性能影响，请参阅 [nfsv 4.1 上 Kerberos 的性能影响](configure-kerberos-encryption.md#kerberos_performance)。 

        ![Kerberos 安全选项](../media/azure-netapp-files/kerberos-security-options.png) 

    * **根访问**  
        指定帐户是否 `root` 可以访问该卷。  默认情况下，根访问权限设置为 **On**，并且该 `root` 帐户有权访问该卷。

![导出策略](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>后续步骤 
* [为虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照](azure-netapp-files-manage-snapshots.md)
