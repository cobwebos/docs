---
title: include 文件
description: include 文件
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060524"
---
**Name** | **商用 URL**  | **政府 URL** | **说明** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | 用于使用 AAD 的访问控制和标识管理 
备份 | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | 用于复制数据传输和协调 
复制 | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | 用于复制管理操作和协调 
存储 | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | 用于访问存储所复制数据的存储帐户 
遥测（可选） | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | 用于遥测 
时间同步 | ``time.windows.com`` | ``time.nist.gov`` | 用于检查所有部署中的系统时间与全球时间之间的时间同步。


