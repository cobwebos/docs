---
title: 参考：Data Science Virtual Machine 映像弃用
titleSuffix: Azure Data Science Virtual Machine
description: 有关影响 Azure Data Science Virtual Machine 的弃用的详细信息
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754768"
---
# <a name="reference-deprecation-of-dsvm-images"></a>参考：DSVM 映像的弃用

下文将讨论有关如何处理 Azure Data Science Virtual Machine 上即将发布的弃用的建议。

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012：迁移数据磁盘

我们将于 2019 年 12 月 31 日停止支持 Windows 2012 DSVM 映像。 若要将数据磁盘从现有的 Windows 2012 DSVM 迁移到 Windows 2016 DSVM，请执行以下步骤：

1. 按照[此处](./provision-vm.md#create-your-dsvm)显示的说明，创建新的 Windows 2016 DSVM。
1. 根据[这些说明](../../virtual-machines/windows/detach-disk.md)，将现有数据磁盘从 Windows 2012 映像中拆离。
1. 根据[这些说明](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)，将上一步骤中的磁盘附加到 Windows 2016 映像。

## <a name="centos"></a>CentOS

新用户应使用最新的 Ubuntu 或 Windows 映像。 CentOS 仍可与现有解决方案模板一起使用。