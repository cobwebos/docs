---
title: 注册 Azure VMware 解决方案资源提供程序
description: 注册 Azure VMware 解决方案资源提供程序的步骤。
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575735"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

若要使用 Azure VMware 解决方案，必须首先将资源提供程序注册到你的订阅。

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>或者，可以使用 GUI 来注册 Microsoft.AVS 资源提供程序。  有关详细信息，请参阅[注册资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)一文。  
