---
title: include 文件
description: include 文件
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "67172632"
---
## <a name="enable-event-grid-resource-provider"></a>启用事件网格资源提供程序

如果以前未在 Azure 订阅中使用过事件网格，则可能需要注册事件网格资源提供程序。 运行以下命令，注册提供程序：

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

完成注册可能需要一些时间。 若要检查状态，请运行：

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

当 `registrationState` 为 `Registered` 后，即可继续。