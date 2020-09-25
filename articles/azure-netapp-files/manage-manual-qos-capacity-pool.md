---
title: 管理 Azure NetApp 文件的手动 QoS 容量池 |Microsoft Docs
description: 介绍如何管理使用手动 qos 类型的容量池，包括设置手动 QoS 容量池和更改容量池以使用手动 QoS。
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 1bbd6ad9e33aab8d3564865b86485d70df5a108f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341378"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>管理手动 QoS 容量池

本文介绍如何管理使用手动 QoS 类型的容量池。  

请参阅 [Azure Netapp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 和 [azure Netapp 文件的性能注意事项](azure-netapp-files-performance-considerations.md) ，了解有关 QoS 类型的注意事项。  

## <a name="register-the-feature"></a>注册功能
手动 QoS 类型功能目前处于预览阶段。 如果是首次使用此功能，则需要首先注册该功能。
  
1.  注册功能：

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. 检查功能注册的状态： 

    > [!NOTE]
    > 在**RegistrationState**将 `Registering` 更改为之前，RegistrationState 的状态可能最长为60分钟 `Registered` 。 等到状态 **注册** 后再继续。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
你还可以使用 [Azure CLI 命令](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` 并 `az feature show` 注册功能并显示注册状态。 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>设置新的手动 QoS 容量池 

使用手动 QoS 类型创建新的容量池：

1. 按照 [设置容量池中](azure-netapp-files-set-up-capacity-pool.md)的步骤进行操作。  

2. 在 "新建容量池" 窗口中，选择 " **手动 QoS** " 类型。  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>更改容量池以使用手动 QoS

可以将当前使用 "自动 QoS" 类型的容量池更改为使用手动 QoS 类型。  

> [!IMPORTANT]
> 将容量类型设置为手动 QoS 是永久性更改。 不能将手动 QoS 类型容量工具转换为自动 QoS 容量池。 

1. 在你的 NetApp 帐户的管理边栏选项卡中，单击 " **容量池** " 显示现有容量池。   
 
2.  单击要更改为使用手动 QoS 的容量池。

3.  单击 " **更改 QoS 类型**"。 然后，将 **新的 QoS 类型** 设置为 " **手动**"。 单击“确定”。 

![更改 QoS 类型](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>监视手动 QoS 容量池的吞吐量  

指标可用于帮助监视卷的读取和写入吞吐量。  请参阅 [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)。  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>修改手动 QoS 卷的分配吞吐量 

如果卷包含在手动 QoS 容量池中，则可以根据需要修改分配的卷吞吐量。

1. 从 " **卷** " 页中，选择要修改其吞吐量的卷。   

2. 单击 " **更改吞吐量**"。 指定所需 **)  (MiB/秒的吞吐量 ** 。 单击“确定”。 

    ![更改 QoS 吞吐量](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>后续步骤  

* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)
* [Azure NetApp 文件的性能考虑因素](azure-netapp-files-performance-considerations.md)
* [排查容量池问题](troubleshoot-capacity-pools.md)
