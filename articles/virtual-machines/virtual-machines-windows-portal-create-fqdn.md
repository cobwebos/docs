---
title: "在 Azure 门户中为 Windows VM 创建 FQDN | Microsoft 文档"
description: "了解如何在 Azure 门户中为基于 Resource Manager 的虚拟机创建完全限定域名或 FQDN。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 102/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 746ed119239d5a0a71a0663e81d9c5b259354419
ms.openlocfilehash: e89e5769701f00c6f403bd54cb2b29bcce53bf15


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>在 Azure 门户中创建完全限定的域名
在 [Azure 门户](https://portal.azure.com)中使用 Resource Manager 部署模型创建虚拟机 (VM) 时，该门户会为虚拟机自动创建一个公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然默认情况下门户不创建[完全限定域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)（即 FQDN），但可以在创建 VM 后创建一个完全限定域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

你现在可以使用此 DNS 名称（例如远程桌面协议 (RDP)）远程连接至 VM。

## <a name="next-steps"></a>后续步骤
你的 VM 已经有公共 IP 和 DNS 名称，现在可以部署通用应用程序框架或服务，例如 IIS、SQL 或 SharePoint。

你也可以深入了解[使用 Resource Manager](../azure-resource-manager/resource-group-overview.md)，以获取生成 Azure 部署的相关提示。




<!--HONumber=Jan17_HO4-->


