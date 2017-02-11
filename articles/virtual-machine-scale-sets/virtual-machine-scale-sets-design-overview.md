---
title: "设计规模化的虚拟机规模集 | Microsoft Docs"
description: "了解如何设计规模化的虚拟机规模集"
keywords: "linux 虚拟机, 虚拟机缩放集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4821bf5b7e3527e9d130e24c81d90368f395d30


---
# <a name="designing-vm-scale-sets-for-scale"></a>设计规模化的 VM 规模集
本主题讨论虚拟机规模集的设计注意事项。 有关什么是虚拟机规模集的信息，请参阅[虚拟机规模集概述](virtual-machine-scale-sets-overview.md)。

## <a name="storage"></a>存储
规模集使用存储帐户来存储集中 VM 的操作系统磁盘。 我们建议每个存储帐户采用 20 台 VM 或更低的比率。 我们还建议存储帐户名称的开始字符采用字母表的字符。 这样做有助于在多个不同的内部系统中分散负载。 例如，在下面的模板中，我们使用 uniqueString Resource Manager 模板函数来生成附加到存储帐户名称的前缀哈希：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)。

## <a name="overprovisioning"></a>预配过度
从 "2016-03-30" API 版本开始，VM 规模集默认设置为“预配过度”VM。 开启预配过度时，规模集实际上预配的 VM 数量超过了你所要求的，然后删除上一次预配的额外 VM。 预配过度可提高预配成功率。 这些额外的 VM 不会计费，并且不会计入配额限制。

预配过度可以改进预配成功率，但会导致应用程序（其设计目的不是处理突然消失的 VM）行为混乱。 为了关闭预配过度，请确保你的模板中包含以下字符串："overprovision": "false"。 可在 [VM 规模集 REST API 文档](https://msdn.microsoft.com/library/azure/mt589035.aspx)找到更多详细信息。

如果关闭预配过度，每个存储帐户中可以得到更大的 VM 比率，但我们不建议此比率高于 40。

## <a name="limits"></a>限制
基于自定义映像的（由您生成的）规模集必须在一个存储帐户中创建所有操作系统磁盘 VHD。 因此，基于自定义映像的规模集中 VM 的最大建议数目为 20。 如果关闭预配过度，最大可为 40。

基于平台映像的规模集当前被限制为 100 台 VM（我们建议为此规模采用 5 个存储帐户）。

对于高出这些限制所允许的 VM，需要部署多个规模集，如[此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)所示。




<!--HONumber=Nov16_HO3-->


