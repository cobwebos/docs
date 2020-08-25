---
title: 参考： Data Science Virtual Machine 图像 Retirements
titleSuffix: Azure Data Science Virtual Machine
description: 影响 Azure Data Science Virtual Machine 的 retirements 的详细信息
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816314"
---
# <a name="reference-retirements-of-dsvm-images"></a>参考： DSVM 图像的 Retirements

下面讨论了退休 (弃用) 的映像，以及在 Azure Data Science Virtual Machine 处理即将发布的 retirements 的建议。

## <a name="why-we-retire-dsvm-images"></a>为什么我们停用 DSVM 映像

Data Science Virtual Machine 当前有两个版本：

* Ubuntu
* Windows Server

这些版本的 DSVM 映像是基于特定操作系统版本（例如 Ubuntu 18.04 LTS）构建的。 随着时间的推移，操作系统 _版本_ 的维护时段将结束，并且/或数据科学工具将不再支持较早版本的操作系统。 为了使用最新的操作系统和数据科学工具保持 DSVM 映像的最新状态，我们会根据更新的操作系统版本发布新的 DSVM 映像。

## <a name="how-we-retire-dsvm-images"></a>如何停用 DSVM 映像

我们发出 _退休公告_ ，其中现有的 DSVM 用户通过电子邮件) 通知即将停用， (收到通知。 停用通知将详细说明此日期之后 (_停_ 用该映像的日期) 和缓解建议 (例如，升级到较新的 DSVM 映像) 。

在 _发布_ 日期，我们将隐藏 marketplace 中的映像，以便：

1. 禁止新用户无意中设置停用的 DSVM 映像。
2. 现有用户可以使用 ARM 部署，直到停用日期。

隐藏的图像将在 _停用日期_ 之前接收操作系统修补程序，但 __不__ 会接收到数据科学工具和框架的更新。 停用 _日期_后，该映像将无法用于 ARM 部署。

订阅中任何预配的 DSVM 映像将在停用日期后继续运行。 但是，我们建议升级到最新的 DSVM 映像，以便使用最新的操作系统和数据科学工具。

## <a name="impact"></a>影响

订阅中的现有 DSVM 预配映像将在停用日期后继续运行。 但是，我们建议用户使用 Azure 门户或 ARM 模板将其 DSVM 映像升级到较新版本。

> [!WARNING]
> 停用虚拟机规模集后，使用虚拟机规模集预配的 DSVM 映像将无法在停用日期之后扩展。
>
> 尚未使用新的 DSVM 映像详细信息更新的 ARM 模板将无法在停用日期之后部署。

## <a name="mitigating-upcoming-retirements"></a>缓解即将发布的 retirements

在本部分中，我们将讨论缓解即将发布的 retirements。

### <a name="upgrade-windows-2016-dsvm"></a>升级 Windows 2016 DSVM

若要将数据磁盘从现有的 Windows 2016 DSVM 迁移到 Windows 2019 DSVM，请执行以下步骤：

1. 按照 [此处](./provision-vm.md#create-your-dsvm)所示的说明创建新的 WINDOWS 2019 DSVM。
1. 按照 [这些说明](../../virtual-machines/windows/detach-disk.md)，将现有数据磁盘与 Windows 2016 映像分离。
1. 按照 [这些说明](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)，将上一步中的磁盘附加到 Windows 2019 映像。

### <a name="upgrade-ubuntu-1604-dsvm"></a>升级 Ubuntu 16.04 DSVM

建议将现有 Ubuntu 16.04 Dsvm 升级到 [Ubuntu 18.04 DSVM edition](./dsvm-ubuntu-intro.md)。
