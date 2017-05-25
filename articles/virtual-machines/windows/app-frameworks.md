---
title: "在 Azure 的 Windows VM 上部署应用程序框架 | Microsoft Docs"
description: "使用 Azure Resource Manager 模板在 Windows VM 上创建常用应用程序框架，以便安装 Active Directory、Docker，等等。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 67a67141-b095-44ff-bfdf-7311d1c28b89
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 11fa6db637d90c2ee324a472730c607b8ae992c6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="deploy-popular-application-frameworks-on-windows-using-azure-resource-manager-templates"></a>使用 Azure Resource Manager 模板在 Windows 上部署常用的应用程序框架 

工作负荷通常要求多项资源按设计运行。 利用 Azure Resource Manager 模板，你不仅可以定义应用程序的配置方式，而且可以定义资源的部署方式，以便为配置的应用程序提供支持。 本文介绍库中最常用的模板以及如何使用 Azure 门户、Azure CLI 或 PowerShell 来部署它们。

[!INCLUDE [virtual-machines-common-app-frameworks](../../../includes/virtual-machines-common-app-frameworks.md)]


