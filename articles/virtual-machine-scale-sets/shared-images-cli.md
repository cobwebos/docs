---
title: 使用共享 VM 映像在 Azure 中创建规模集
description: 了解如何使用 Azure CLI 在 Azure 中创建用于部署虚拟机规模集的共享 VM 映像。
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 30a376755bf0041d5d6f5cb289f5aecd1a734dc5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792796"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>通过 Azure CLI 2.0 创建和使用虚拟机规模集的共享映像

创建规模集时，需指定部署 VM 实例时要使用的映像。 [共享映像库](shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 

共享图像库使您可以与他人共享图像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>后续步骤

从[VM](../virtual-machines/image-version-vm-cli.md)或[托管映像](../virtual-machines/image-version-managed-image-cli.md)创建映像版本。

有关共享映像库的详细信息，请参阅[概述](shared-image-galleries.md)。 如果遇到问题，请参阅[排查共享映像库问题](troubleshooting-shared-images.md)。
