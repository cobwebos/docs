---
title: include 文件
description: include 文件
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765532"
---
| 名称 | 商用 URL | 政府 URL | 说明 |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | 用于使用 AAD 的访问控制和标识管理 |
| 备份 | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | 用于复制数据传输和协调 |
| 复制 | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | 用于复制管理操作和协调 |
| 存储 | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | 用于访问存储所复制数据的存储帐户 |
| 遥测（可选） | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | 用于遥测 |

``time.nist.gov`` 和 ``time.windows.com`` 用于检查所有部署中的系统时间与全球时间之间的时间同步。


