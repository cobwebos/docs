---
title: 条件访问-按位置阻止访问-Azure Active Directory
description: 创建自定义条件访问策略以阻止 IP 位置访问资源
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b31fc3aed0d412646d9924e87170dffcd5145409
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576597"
---
# <a name="conditional-access-block-access-by-location"></a>条件性访问:按位置阻止访问

使用条件性访问中的位置条件, 可以基于用户的网络位置来控制对云应用的访问。 位置条件通常用于阻止来自组织知道流量不应来自的国家/地区的访问。

## <a name="define-locations"></a>定义位置

1. 以全局管理员、安全管理员或条件访问管理员身份登录到 **Azure 门户** 。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 选择 "**新位置**"。
1. 为你的位置命名。
1. 如果你知道构成该位置或**国家/地区**的特定于外部的可访问 IPv4 地址范围, 请选择 " **IP 范围**"。
   1. 提供**IP 范围**或为指定的位置选择**国家/地区**。
      * 如果选择了 "国家/地区", 则可以选择包含未知区域。
1. 选择**保存**

有关条件访问中的位置条件的详细信息, 请参阅文章中[的位置条件 Azure Active Directory 条件访问中的位置条件](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>创建条件性访问策略

1. 以全局管理员、安全管理员或条件访问管理员身份登录到 **Azure 门户** 。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织为其策略名称创建有意义的标准。
1. 在 "**分配**" 下, 选择 "**用户和组**"
   1. 在 "**包括**" 下, 选择 "**所有用户**"。
   1. 选择“完成”。
1. 在 "**云应用或操作** > **包括**" 下, 选择 "**所有云应用**", 然后选择 "**完成**"。
1. **条件** > **位置**。
   1. 将 "**配置**" 设置为 **"是"**
   1. **包括**选择**所选位置**
   1. 选择为你的组织创建的阻止位置。
   1. 单击 **"选择** > **完成** > "。
1. 在 "**访问控制** > "**块**下, 选择 "**选择**"。
1. 确认设置并将 "**启用策略**" 设置为 **"开"** 。
1. 选择 "**创建**" 以启用策略。

## <a name="next-steps"></a>后续步骤

[条件访问公用策略](concept-conditional-access-policy-common.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
