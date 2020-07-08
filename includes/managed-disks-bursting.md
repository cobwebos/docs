---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242222"
---
在 Azure 上，我们提供了在虚拟机和磁盘上提升磁盘存储 IOPS 和 MB/s 性能的功能。 突发在许多情况下很有用，例如处理意外的磁盘流量或处理批处理作业。 可以有效地利用 VM 和磁盘级别的突发，以在 VM 和磁盘上实现良好的基线性能和突发性能。 这样，你就可以在 VM 和磁盘上都获得良好的基线性能和突发性能。 

请注意，磁盘和 Vm 之间的突发会相互独立。 如果有一个突发磁盘，则不需要使用突发 VM 来允许磁盘突发。 如果有突发 VM，则无需使用突发磁盘来允许 VM 突发。 