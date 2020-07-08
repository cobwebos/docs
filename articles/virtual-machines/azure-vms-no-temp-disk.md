---
title: 不带本地临时磁盘的 Azure VM 大小常见问题
description: 本文提供了有关 Microsoft Azure 没有本地临时磁盘的 VM 大小的常见问题解答（FAQ）的解答。
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 413f53feedc4fee0877694e3f3a3a509c4d38001
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783536"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>不带本地临时磁盘的 Azure VM 大小 
本文提供了有关 Azure VM 大小（即没有本地临时磁盘，即没有本地临时磁盘）的常见问题解答（FAQ）的解答。 有关这些 VM 大小的详细信息，请参阅[Dv4 和 Dsv4 系列的规范（常规用途工作负荷）](dv4-dsv4-series.md)或[Ev4 和 Esv4 系列（内存优化工作负荷）的规范](ev4-esv4-series.md)。

> [!IMPORTANT]
> Dv4、Dsv4、Ev4 和 Esv4 的 VM 大小现为公共预览版。 若要注册公共预览版，请填写此[窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)。 

## <a name="what-does-no-local-temp-disk-mean"></a>本地临时磁盘是不是什么意思？ 
通常，我们有 VM 大小（例如 Standard_D2s_v3、Standard_E48_v3），其中包含一个小型本地磁盘（即 D：驱动器）。 现在，有了这些新的 VM 大小，该小型本地磁盘将不再存在;不过，您仍然可以附加标准 HDD、高级 SSD 或超级 SSD。

## <a name="what-if-i-still-want-local-temp-disk"></a>如果仍需要本地临时磁盘怎么办？
如果工作负荷需要本地临时磁盘，还可以使用新的[Ddv4、Ddsv4](ddv4-ddsv4-series.md)或[Edv4 和 Edsv4](edv4-edsv4-series.md) VM 大小。 与以前的 v3 大小相比，这些大小提供了50% 的临时磁盘。

> [!NOTE]
> 本地临时磁盘不持久;若要确保数据是永久性的，请使用标准 HDD、高级 SSD 或超级 SSD 选项。 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>这些新 VM 大小与常规用途 Dv3/Dsv3 或我使用的内存优化 Ev3/Esv3 VM 大小之间有何区别？ 
| v3 VM 家族与本地临时磁盘   | 带有本地临时磁盘的新 v4 系列 | 不带本地临时磁盘的新 v4 系列 |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>是否可以将具有本地临时磁盘的 VM 大小调整为不带本地临时磁盘的 VM 大小？  
不能。 允许调整大小的唯一组合如下： 

1. VM （包含本地临时磁盘）-> VM （包含本地临时磁盘）;与 
2. VM （没有本地临时磁盘）-> VM （没有本地临时磁盘）。 

> [!NOTE]
> 如果映像依赖于资源磁盘，或者本地临时磁盘上存在页面文件或交换文件，则无盘映像将不起作用-改为使用 "使用磁盘" 替代项。 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>这些 VM 大小是否支持 Linux 和 Windows 操作系统（OS）？
是的。

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>这是否会中断自定义脚本、自定义映像或在本地临时磁盘上具有暂存文件或页面文件的操作系统映像？
如果自定义 OS 映像指向本地临时磁盘，则该映像可能无法正常使用此无磁盘大小。

## <a name="have-questions-or-feedback"></a>还有任何疑问或反馈？
填写[反馈窗体]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u)。 

## <a name="next-steps"></a>后续步骤 
在本文档中，你了解了有关具有本地临时磁盘的 Azure Vm 的最常见问题的详细信息。 有关这些 VM 大小的详细信息，请参阅以下文章：

- [Dv4 和 Dsv4 系列的规格（常规用途工作负荷）](dv4-dsv4-series.md)
- [Ev4 和 Esv4 系列的规范（内存优化工作负荷）](ev4-esv4-series.md)
