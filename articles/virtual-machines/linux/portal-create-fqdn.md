---
title: "在 Azure 门户中为 Linux VM 创建 FQDN | Microsoft 文档"
description: "了解如何在 Azure 门户中为基于 Resource Manager 的虚拟机创建完全限定域名或 FQDN。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: a672b0afbc622890843e056883cb437749d3cff9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>在 Azure 门户中为 Linux VM 创建完全限定的域名

在 [Azure 门户](https://portal.azure.com)中创建虚拟机 (VM) 时，此门户会自动为虚拟机创建公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然此门户不会创建[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)，但可以在创建 VM 后添加完全限定的域名。 本文演示了创建 DNS 名称或 FQDN 的步骤。

## <a name="create-fqdn"></a>创建 FQDN
阅读本文的前提是已创建 VM。 如果需要，可以[在门户中创建 VM](quick-create-portal.md)，也可以[使用 Azure CLI 创建 VM](quick-create-cli.md)。 在 VM 正常运行后，请按照以下步骤操作：

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

现在可以使用此 DNS 名称（例如搭配 `ssh azureuser@mydns.westus.cloudapp.azure.com`）远程连接至 VM。

## <a name="next-steps"></a>后续步骤
你的 VM 已经有公共 IP 和 DNS 名称，现在可以部署通用应用程序框架或服务，例如 nginx、MongoDB、Docker 等等。

你也可以深入了解[使用 Resource Manager](../../azure-resource-manager/resource-group-overview.md)，以获取生成 Azure 部署的相关提示。


