---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230945"
---
最新一代的 VM 大小支持在主机上加密：

|类型  |不支持  |支持  |
|---------|---------|---------|
|常规用途     | Dv3、Dav4、Dv2、Av2        | B、DSv2、Dsv3、DC、DCv2、Dasv4        |
|计算优化     |         | Fsv2        |
|内存优化     | Ev3、Eav4        | DSv2、Esv3、M、Mv2、Easv4        |
|存储优化     |         | Ls、Lsv2 (NVMe 磁盘未加密)         |
|GPU     | NC，NV        | NCv2、NCv3、ND、NVv3、NVv4、NDv2 (preview)         |
|高性能计算     | H        | HB-ACCT-WC、HC、HBv2        |
|前几代     | F、A、D、L、G        | DS、GS、Fs、NVv2        |

升级 VM 大小将导致验证检查新 VM 大小是否支持 EncryptionAtHost 功能。
