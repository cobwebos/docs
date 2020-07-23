---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230948"
---
- 不支持 ultra 磁盘。
- 如果在 Vm/虚拟机规模集上启用了 Azure 磁盘加密 (使用 bitlocker/VM 解密) 的来宾 VM 加密，则无法启用。
- 无法在启用了主机加密的磁盘上启用 Azure 磁盘加密。
- 可以在现有虚拟机规模集上启用加密。 不过，只有在启用加密后创建的新 Vm 才会自动加密。
- 必须解除分配和重新分配现有 Vm，才能对其进行加密。