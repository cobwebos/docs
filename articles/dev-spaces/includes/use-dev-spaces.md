---
title: include 文件
description: include 文件
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 2563f7c36283521541562bcd88f973d86a6f672a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198980"
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>配置 AKS 群集以使用 Azure Dev Spaces

打开命令窗口，并使用包含 AKS 群集的资源组和 AKS 群集名称输入以下 Azure CLI 命令：

   ```cmd
   az extension add --name dev-spaces-preview 
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
第一个命令安装 Azure CLI 的扩展以添加对 Azure Dev Spaces 的支持，第二个命令配置群集以支持 Azure Dev Spaces。
