---
title: "来自多个地理区域的登录"
description: "指示用户的报告，其中两次登录显示为来自不同区域且这两次登录间隔的时间不足以让用户从其中一个区域前往另一个区域。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 79259c8a-2388-4747-b41e-c07434ea9a02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1de57f64692ade442f9ef8d1e3b587ffee35d7cf
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# 从多个地理区域登录
<a id="sign-ins-from-multiple-geographies" class="xliff"></a>
此报告包括某个用户的成功登录，其中两次登录显示为来自不同区域且这两次登录间隔的时间不足以让用户从其中一个区域前往另一个区域。 可能的原因包括：

* 用户与其他用户共享其密码
* 用户使用远程桌面启动 Web 浏览器进行登录
* 黑客已从不同国家/地区登录到该用户帐户
* 用户使用的是 VPN 或代理
* 用户同时从多台设备（例如台式计算机和移动电话）登录，并且移动电话的 IP 地址异常。

来自此报告的结果将为你显示成功的登录事件以及两次登录间隔的时间、这些登录似乎源自的区域以及在这两个区域之间往返所需的估计时间。 显示的往返时间仅是估计值，可能不同于在这两个地点之间往返所需的实际时间。

![来自多个地理区域的登录](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)


