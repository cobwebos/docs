---
title: 适用于 Avere vFXT for Azure 的端口允许列表
description: Avere vFXT for Azure 使用的端口列表
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669924"
---
# <a name="required-ports"></a>必需端口

本节中列出的端口用于 vFXT 入站和出站通信。

切勿将 vFXT 群集或群集控制器实例直接暴露给公共 Internet。

## <a name="api"></a>API

| 入站： | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| 出站： |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| 入站和出站  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | 装载   |
| TCP/UDP | 4047 | 状态   |

