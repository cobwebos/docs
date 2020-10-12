---
title: 使用 Azure CLI 创建共享映像库
description: 在本文中，你将了解如何使用 Azure CLI 在 Azure 中创建 VM 的共享映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff50f61fe159c518d488da9f9911e40f1ddb8ca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501451"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>使用 Azure CLI 创建共享映像库

[共享映像库](./linux/shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 

共享映像库可让你与他人共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>后续步骤

使用 Azure CLI 从 [VM](image-version-vm-cli.md) 或[托管映像](image-version-managed-image-cli.md)创建映像版本。

有关共享映像库的详细信息，请参阅[概述](./linux/shared-image-galleries.md)。 如果遇到问题，请参阅[排查共享映像库问题](troubleshooting-shared-images.md)。
