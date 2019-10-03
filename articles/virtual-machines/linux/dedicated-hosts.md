---
title: 适用于虚拟机的 Azure 专用主机概述 |Microsoft Docs
description: 了解有关如何将 Azure 专用主机用于部署虚拟机的详细信息。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 5f2b34b3acb559d74414ea622fba2769ede7f0a7
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976645"
---
# <a name="preview-azure-dedicated-hosts"></a>预览版：Azure 专用主机

Azure 专用主机是一项服务, 可提供物理服务器, 这些服务器可以承载一个或多个虚拟机-专用于一个 Azure 订阅。 专用主机是数据中心中使用的物理服务器, 作为资源提供。 你可以在区域、可用性区域和容错域中预配专用主机。 然后, 你可以将 Vm 直接置于预配的主机中, 这种配置最适合你的需求。

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


virtual-machines-common-dedicated-hosts-preview.md

## <a name="next-steps"></a>后续步骤

- 你可以使用[Azure CLI](dedicated-hosts-cli.md)、[门户](dedicated-hosts-portal.md)和[PowerShell](../windows/dedicated-hosts-powershell.md)部署专用主机。

- 有关详细信息, 请参阅[专用主机](dedicated-hosts.md)概述。

- [这里](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板, 它使用区域和容错域实现了区域中的最大复原能力。