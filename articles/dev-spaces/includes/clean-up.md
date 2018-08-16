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
ms.openlocfilehash: b8b8b1a593328197c61f8edba9f8f849518dd2e5
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129078"
---
## <a name="clean-up"></a>清理
若要完全删除群集中的某个 Azure Dev Spaces 实例，包括所有设备空间以及其中正在运行的服务，请使用 `az aks remove-dev-spaces` 命令。 请记住，此操作是不可逆的。 可以再次在群集上添加 Azure Dev Spaces 支持，但这就像重新开始一样。 旧服务和空间不会还原。

下面的示例列出你的活动订阅中的 Azure Dev Spaces 控制器，然后删除与资源组“myaks-rg”中的 AKS 群集“myaks”关联的 Azure Dev Spaces 控制器。

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```

