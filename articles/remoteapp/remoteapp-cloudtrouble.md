---
title: "排查 RemoteApp 云集合 - 创建 | Microsoft Docs"
description: "了解如何排查 RemoteApp 云集合创建失败问题"
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: 95eb7797-7b5e-4781-ad23-f15dd85b213d
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 304ba7c5057b27c459bccbb75d3a711567757675
ms.lasthandoff: 03/31/2017


---
# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>排查创建 RemoteApp 云集合时遇到的问题
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

如果在创建云集合时遇到问题，请查看以下信息。

## <a name="your-image-is-invalid"></a>你的映像无效
如果在等待 Azure 设置你的集合时，你看到类似“GoldImageInvalid”的消息，这意味着你的模板映像不符合[定义的映像要求](remoteapp-imagereqs.md)。 因此，请继续阅读这些[要求](remoteapp-imagereqs.md)、修复你的映像，并尝试再次创建你的集合。

## <a name="common-errors-seen-in-the-azure-management-portal"></a>在 Azure 管理门户中看到的常见错误
    DNS server could not be reached
    ProvisioningTimeout

由于你使用自定义映像，云集合在创建过程中经常失败。  如果你看到上述错误之一，并且正在使用自定义映像创建集合，请检查下列事项：

* 请确保你上载的自定义映像符合映像要求。
* 大多数情况下，常见问题是映像未正确地经过 sysprep 处理。  
* 验证该映像可以在 Hyper-V 内引导或者尝试使用该映像直接在你的 Azure 订阅中创建 IAAS VM。 如果 VM 无法引导且无法启动，则通常表示自定义映像未正确准备。  验证自定义映像是否是遵循“如何为 RemoteApp 创建自定义模板映像”生成的

如果使用的是你的订阅附带的 Microsoft 映像之一，请尝试再次创建集合。 如果该问题仍然存在，请联系 Microsoft 支持部门。

    PlatformImageTrialModeOnly

如果你看到此错误，这通常表示你已升级到付费帐户，但是你正在尝试使用仅在该服务的试用模式期间 Microsoft 提供的有效映像。 在这种情况下，尝试再次创建你的云集合，但是请务必指定正确的映像。


