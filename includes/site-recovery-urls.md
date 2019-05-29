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
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116020"
---
名称 | 商用 URL | 政府 URL | 说明
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | 由 Azure Active Directory 用于访问控制和标识管理。 
备份 | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | 用于复制数据传输和协调。
复制 | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | 用于复制管理操作和协调。
存储 | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | 用于访问存储所复制数据的存储帐户。
遥测（可选） | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | 用于遥测。
时间同步 | ``time.windows.com`` | ``time.nist.gov`` | 用于检查所有部署中的系统时间与全球时间之间的时间同步。


