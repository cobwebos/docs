---
title: "比较开发测试实验室中的自定义映像和公式 | Microsoft Docs"
description: "了解自定义映像和公式（例如 VM 基项）之间的差异，以便确定哪一种最适合自己的环境。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08871f5984697370cefe47efa597f7e067f32ae3


---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>比较自定义映像和 DevTest 实验室中的公式
## <a name="overview"></a>概述
在[创建新的虚拟机](devtest-lab-add-vm-with-artifacts.md)时，[自定义映像](devtest-lab-create-template.md) 和 [公式](devtest-lab-manage-formulas.md) 都可以用作基项。 但是，两者之间的主要区别在于自定义映像只是基于 VHD的映像，然而公式不仅是基于 VHD 的映像，还有预配置的设置 - 如 VM 大小、虚拟网络和子网、项目等。 使用可在创建 VM 时重写的默认值，对这些预配置进行设置。 本文介绍和使用公式相比，使用自定义映像的一些优点和缺点。

## <a name="custom-image-pros-and-cons"></a>自定义映像得优点和缺点
自定义映像以静态、不可变的方式，从所需的环境中创建虚拟机。 

**优点**

* 从映像创建虚拟机后，一切都没有改变，因此预配速度很快。 换而言之，没有要应用的设置，因为自定义映像就是无需设置的映像。 
* 从单一的自定义映像创建的 VM 都是相同的。

**缺点**

* 如果需要更新自定义映像的某些方面，则必须重新创建映像。  

## <a name="formula-pros-and-cons"></a>公式的优缺点
公式提供了灵活的方式，从所需的配置或设置创建虚拟机。

**优点**

* 可以通过项目快速捕获环境中的更改。 例如，如果想安装拥有发布管道最新位的 VM 或登记存储库中的最新代码，只需指定部署最新位或登记公式中的最新代码及目标基本映像的项目。 只要此公式用于创建虚拟机，最新的位或代码就会部署/登记到 VM。 
* 公式可以定义默认设置，而自定义映像则不能提供 - 如 VM 大小和虚拟网络设置等。 
* 保存在公式中的设置显示如下所示的默认值，但创建 VM 时可以进行修改。 

**缺点**

* 相比从自定义映像创建虚拟机，从公式创建虚拟机可能需要更多的时间。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [自定义映像或公式吗？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)




<!--HONumber=Nov16_HO3-->


