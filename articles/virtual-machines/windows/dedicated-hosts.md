---
title: 虚拟机 Azure 专用主机概述
description: 详细了解如何使用 Azure 专用主机部署虚拟机。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 9709408f006cbcc5786a4e5a854abcd20affd693
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082826"
---
# <a name="azure-dedicated-hosts"></a>Azure 专用主机

Azure 专用主机是一种服务，它提供物理服务器（能够承载一个或多个虚拟机）专用于一个 Azure 订阅。 专用主机是作为资源提供的数据中心中使用的物理服务器。 您可以在区域、可用性区域和容错域中预配专用主机。 然后，您可以将 VM 直接放入预配主机中，无论配置最适合您的需要。


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>后续步骤

- 可以使用[Azure PowerShell](dedicated-hosts-powershell.md)、[门户](dedicated-hosts-portal.md)和[Azure CLI](../linux/dedicated-hosts-cli.md)部署专用主机。

- [此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例模板，它同时使用区域和容错域，以实现区域中的最大恢复能力。

- 您还可以使用[Azure 专用主机的预留实例](../prepay-dedicated-hosts-reserved-instances.md)来节省成本。
