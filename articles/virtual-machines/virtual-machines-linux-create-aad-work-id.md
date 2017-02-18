---
title: "在 AAD 中创建适用于 Linux 的工作或学校标识 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中创建工作或学校标识以用于 Linux 虚拟机。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: b0f86d77-c669-4aa1-a095-c2aa4d9105fe
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 1d35114800ba024d0c201840722df2c6a74d1d61
ms.openlocfilehash: 0eaa83fc19316708685eed0ca994112bc09153e5


---
# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>在 Azure Active Directory 中创建工作或学校标识，以便用于 Linux VM
如果创建了个人 Azure 帐户或者具有个人 MSDN 订阅并且创建了 Azure 帐户以利用 MSDN Azure 信用额度，则你已使用 *Microsoft 帐户*标识来创建它。 Azure 的许多强大功能 - [资源组模板](../azure-resource-manager/resource-group-overview.md)就是一个例子 - 需要有工作或学校帐户（由 Azure Active Directory 管理的标识）才能运行。 你可以遵循以下说明创建新的工作帐户或学校帐户，因为在个人 Azure 帐户方面，最有利的特点之一是，这种帐户附带了一个默认 Azure Active Directory 域，使用该域可以创建新的工作或学校帐户，以用于需要这种帐户的 Azure 功能。

但是，利用最近所做的更改，可以使用[此处](../xplat-cli-connect.md)所述的 `azure login` 交互式登录方法，通过任何类型的 Azure 帐户管理你的订阅。 你可以使用该机制，也可以遵循后面的说明。 还可以[在 Azure Active Directory 中创建工作或学校标识以用于 Windows VM](virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]




<!--HONumber=Jan17_HO4-->


