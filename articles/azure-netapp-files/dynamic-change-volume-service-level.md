---
title: 动态更改 Azure NetApp 文件的卷的服务级别 |Microsoft Docs
description: 描述如何动态更改卷的服务级别。
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 5097a5dfa6dd9b8fd46e4bcbcee72319af51f86f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499358"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>动态更改卷的服务级别

可以通过将卷移到使用所需的[服务级别](azure-netapp-files-service-levels.md)的其他容量池来更改现有卷的服务级别。 此卷的就地服务级别更改不需要迁移数据。 它也不会影响对卷的访问。  

此功能可让你满足需求的工作负荷需求。  您可以更改现有卷以使用较高的服务级别来提高性能，或使用较低的服务级别进行成本优化。 例如，如果卷当前位于使用*标准*服务级别的容量池中，并且你希望卷使用*高级*服务级别，则可以将该卷动态移到使用*高级*服务级别的容量池。  

要移动卷的容量池必须已经存在。 容量池可以包含其他卷。  若要将卷移动到全新容量池，需要在移动卷之前[创建容量池](azure-netapp-files-set-up-capacity-pool.md)。  

## <a name="considerations"></a>注意事项

* 将卷移动到另一个容量池后，将无法再访问以前的卷活动日志和卷指标。 卷将从新的容量池下的新活动日志和指标开始。

* 如果将卷移动到更高的服务级别（例如，从*标准*版升级到高级服务级别或*高级*服务级别），则必须等待至少7天，然后才能将该卷再次移到较低*的服务级别*的容量池（例如，从*超小型*迁移到*高级*或*标准*版）。  
如果将卷移动到具有相同服务级别或更低服务级别的容量池，则不会应用此等待期限。

## <a name="register-the-feature"></a>注册功能

1. 将卷移动到另一个容量池的功能当前处于预览阶段。 如果这是你第一次使用此功能，请在使用此功能前注册它： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. 检查功能注册的状态： 

    > [!NOTE]
    > 在将更改为之前， **RegistrationState**可能处于 `Registering` 状态几分钟 `Registered` 。 等到状态**注册**后再继续。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

## <a name="move-a-volume-to-another-capacity-pool"></a>将卷移动到另一个容量池

1.  在 "卷" 页上，右键单击要更改其服务级别的卷。 选择 "**更改池**"。

    ![右键单击 "卷"](../media/azure-netapp-files/right-click-volume.png)

2. 在 "更改池" 窗口中，选择要将卷移动到的容量池。 

    ![更改池](../media/azure-netapp-files/change-pool.png)

3.  单击 **“确定”** 。


## <a name="next-steps"></a>后续步骤  

* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
