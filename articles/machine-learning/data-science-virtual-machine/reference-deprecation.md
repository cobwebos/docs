---
title: 参考： DSVM 图像弃用
description: 影响 Azure Data Science Virtual Machine （DSVM）的弃用功能的详细信息
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456288"
---
# <a name="reference-deprecation-of-dsvm-images"></a>参考：弃用 DSVM 映像

下面讨论了在 Azure Data Science Virtual Machine 上处理即将发布的弃用功能的建议。

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012：迁移数据磁盘

我们将在2019年12月31日停止支持 Windows 2012 DSVM 映像。 若要将数据磁盘从现有的 Windows 2012 DSVM 迁移到 Windows 2016 DSVM，请执行以下步骤：

1. 按照[此处](./provision-vm.md#create-your-dsvm)所示的说明创建新的 WINDOWS 2016 DSVM。
1. 按照[这些说明](../../virtual-machines/windows/detach-disk.md)，将现有数据磁盘与 Windows 2012 映像分离。
1. 按照[这些说明](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)，将上一步中的磁盘附加到 Windows 2016 映像。
