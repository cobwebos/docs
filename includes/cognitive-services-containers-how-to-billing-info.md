---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598917"
---
查询到该容器在定价层用于 Azure 资源的计费`<ApiKey>`。

认知服务容器未获得运行无需连接到计费终结点进行计数的许可。 客户需要使容器能够通信与计费终结点在所有时间的计费信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。 

### <a name="connecting-to-azure"></a>连接到 Azure

容器需要计费的参数值，才能运行。 这些值允许用于连接到计费终结点的容器。 容器约每 10 到 15 分钟报告一次使用情况。 如果容器不能连接到 Azure 允许的时间范围内，容器将继续运行，但将不提供查询，直到还原计费终结点。 在 10 到 15 分钟的时间间隔相同 10 次尝试连接。 如果它无法连接到 10 次尝试中的计费终结点，容器将停止运行。 

### <a name="billing-arguments"></a>计费参数

所有这三个以下选项必须使用有效的值，以便指定`docker run`命令来启动该容器：

| 选项 | 描述 |
|--------|-------------|
| `ApiKey` | 用于跟踪账单信息的认知服务资源的 API 密钥。<br/>必须将此选项的值设置为 `Billing` 中指定的已预配资源的 API 密钥。 |
| `Billing` | 用于跟踪账单信息的认知服务资源的终结点。<br/>此选项的值必须设置为终结点的预配 Azure 资源的 URI。|
| `Eula` | 表示已接受容器的许可条款。<br/>此选项的值必须设置为 `accept`。 |


