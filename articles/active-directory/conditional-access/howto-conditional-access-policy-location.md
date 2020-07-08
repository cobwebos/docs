---
title: 条件访问 - 按位置阻止访问 - Azure Active Directory
description: 创建自定义条件访问策略以按 IP 位置阻止访问资源
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5350e728ad9c30a6e9258ce9c7615baf020473a8
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995116"
---
# <a name="conditional-access-block-access-by-location"></a>条件访问：按位置阻止访问

在条件访问中使用位置条件，可以基于用户的网络位置来控制对云应用的访问。 位置条件通常用于阻止来自组织已知不应有流量的国家/地区的访问。

## <a name="define-locations"></a>定义位置

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure 门户。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问” > “命名位置”。
1. 选择“新建位置”。
1. 为位置命名。
1. 如果你知道构成该位置或“国家/地区”的特定外部可访问 IPv4 地址范围，请选择“IP 范围”。 
   1. 为要指定的位置提供“IP 范围”或选择“国家/地区”。 
      * 如果选择“国家/地区”，可以选择包含未知区域。
1. 选择“保存”

可以在 [Azure Active Directory 条件访问中的位置条件是什么](location-condition.md)一文中找到有关在条件访问中位置条件的详细信息

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure 门户。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定一个名称。 建议组织创建一个有意义的策略名称标准。
1. 在“分配”下，选择“用户和组”
   1. 在“包括”下，选择“所有用户”。
   1. 选择“完成”。
1. 在“云应用或操作” > “包含”下方，选择“所有云应用” ，然后选择“完成”。
1. 在“条件” > “位置”下方。
   1. 将“配置”设置为“是” 
   1. 对于“包含”，选择“所选位置” 
   1. 选择你为组织创建的阻止位置。
   1. 单击“选择” > “完成” > “完成”。
1. 在“条件” > “客户端应用(预览)”下，将“配置”设置为“是”，然后选择“完成”。
1. 在“访问控制” >  “阻止”下方，选择“选择”。
1. 确认设置并将“启用策略”设置为“开启”。
1. 选择“创建”以创建并启用策略。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-report-only.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
