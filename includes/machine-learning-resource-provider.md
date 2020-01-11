---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893975"
---
创建 Azure 机器学习工作区或工作区使用的资源时，可能会收到类似于以下消息的错误：

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

大多数资源提供程序均已自动注册，但并非全部。 如果收到此消息，则需要注册所述的提供程序。

有关注册资源提供程序的信息，请参阅[解决资源提供程序注册错误](../articles/azure-resource-manager/templates/error-register-resource-provider.md)。