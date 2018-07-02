---
title: include 文件
description: include 文件
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: c0b9f9e9808de90df84edf2d3c409a921629baee
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055041"
---
如果想要继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。

否则，可删除本文中创建的本地配置和 Azure 资源，避免收费。 

> [!IMPORTANT]
> 删除 Azure 资源和资源组的操作不可逆。 资源组以及包含在其中的所有资源一旦删除就会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要只删除 IoT 中心，请执行以下命令（将 `<YourIoTHub>` 替换为你的中心名称，将 `<TestResources>` 替换为你的资源组名称）：

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


若要按名称删除整个资源组，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

2. 在“按名称筛选...”文本框中键入包含 IoT 中心的资源组的名称。 

3. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。

    ![删除](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. 系统会要求确认是否删除资源组。 再次键入资源组的名称进行确认，然后单击“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。






