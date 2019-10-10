---
title: 条件访问 - 阻止按位置访问 - Azure Active Directory
description: 创建自定义条件访问策略，用于按 IP 位置阻止对资源的访问
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
ms.openlocfilehash: c782c8bb2807017053375b45560685acf78161e7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169973"
---
# <a name="conditional-access-block-access-by-location"></a>条件访问：按位置阻止访问

在条件访问中使用位置条件，可以基于用户的网络位置来控制对云应用的访问。 位置条件通常用于阻止来自特定国家/地区的访问，你的组织知道流量不应该来自这些国家/地区。

## <a name="define-locations"></a>定义位置

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “条件访问”。
1. 选择“新建位置”。
1. 为位置命名。
1. 选择“IP 范围”，前提是你知道特定的可以从外部访问的 IPv4 地址范围，这些范围构成了该位置或“国家/地区”。
   1. 提供“IP 范围”，或者选择“国家/地区”作为要指定的位置。
      * 如果选择“国家/地区”，可以选择包括未知区域。
1. 选择“保存”

若要详细了解条件访问中的位置条件，可参阅 [Azure Active Directory 条件访问中的位置条件是什么](location-condition.md)一文

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择“所有用户”。
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，选择“所有云应用”，然后选择“完成”。
1. 在“条件” > “位置”下，
   1. 将“配置”设置为“是”
   1. 在“包括”下，选择“选定位置”
   1. 选择为组织创建的被阻止位置。
   1. 单击“选择” > “完成” > “完成”。
1. 在“访问控制” > “阻止”下，选择“选择”。
1. 确认设置，然后将“启用策略”设置为“打开”。
1. 选择“创建”，以便创建启用策略所需的项目。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
