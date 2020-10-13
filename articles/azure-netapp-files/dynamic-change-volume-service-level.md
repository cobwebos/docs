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
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: 3f5627f4bd0d62a70ef9d7809f0bf1441141f4c6
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931239"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>动态更改卷的服务级别

可以通过将卷移到使用所需的 [服务级别](azure-netapp-files-service-levels.md) 的其他容量池来更改现有卷的服务级别。 这种针对卷的就地服务级别更改不需要迁移数据， 它也不会影响对卷的访问。  

此功能可让你满足需求的工作负荷需求。  你可以将现有卷更改为使用更高的服务级别（提升性能），或使用更低的服务级别（优化成本）。 例如，如果卷当前位于使用 *标准* 服务级别的容量池中，并且你希望卷使用 *高级* 服务级别，则可以将该卷动态移到使用 *高级* 服务级别的容量池。  

要移动卷的容量池必须已经存在。 容量池可以包含其他卷。  若要将卷移动到全新容量池，需要在移动卷之前 [创建容量池](azure-netapp-files-set-up-capacity-pool.md) 。  

## <a name="considerations"></a>注意事项

* 将卷移动到另一个容量池后，将无法再访问以前的卷活动日志和卷指标。 卷将从新的容量池下的新活动日志和指标开始。

* 如果将卷移动到更高的服务级别的容量池 (例如，从 "*标准*" 层或 "高级" 服务级别) 移动到 "高级" 或 "*高级*" 服务级别时，必须等待至少7天，然后才能将该卷*再次*移到较低*的服务级别*的容量池 (例如，从 " *Ultra* *" 或 "* *标准*") 移动。  

## <a name="register-the-feature"></a>注册功能

将卷移动到另一个容量池的功能当前处于预览阶段。 如果是首次使用此功能，则需要首先注册该功能。

1. 注册功能： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. 检查功能注册的状态： 

    > [!NOTE]
    > 在**RegistrationState**将 `Registering` 更改为之前，RegistrationState 的状态可能最长为60分钟 `Registered` 。 等到状态 **注册** 后再继续。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
你还可以使用 [Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 并 `az feature show` 注册功能并显示注册状态。 

## <a name="move-a-volume-to-another-capacity-pool"></a>将卷移动到另一个容量池

1.  在 "卷" 页上，右键单击要更改其服务级别的卷。 选择 " **更改池**"。

    ![右键单击 "卷"](../media/azure-netapp-files/right-click-volume.png)

2. 在 "更改池" 窗口中，选择要将卷移动到的容量池。 

    ![更改池](../media/azure-netapp-files/change-pool.png)

3.  单击 **“确定”** 。


## <a name="next-steps"></a>后续步骤  

* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)