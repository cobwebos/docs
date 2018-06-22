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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823174"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>配置 AKS 群集以使用 Azure Dev Spaces

打开命令窗口，并输入以下 Azure CLI 命令，同时使用包含 AKS 群集的资源组和 AKS 群集名称。 此命令将群集配置为支持 Azure Dev Spaces。

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

