---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522498"
---
## <a name="validate-container-is-running"></a>验证容器是否正在运行 

有多种方法来验证容器正在运行： 

|请求|用途|
|--|--|
|`http://localhost:5000/`|容器提供了主页。|
|`http://localhost:5000/status`|使用 GET 请求，以验证容器正在运行而不会导致终结点查询。 这可以用于 Kubernetes[活跃度和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。|
|`http://localhost:5000/swagger`|容器提供终结点以及 `Try it now` 功能的整套文档。 通过此功能可以将设置输入到基于 Web 的 HTML 窗体中并进行查询，而无需编写任何代码。 返回查询后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。 |

![容器的主页](./media/cognitive-services-containers-api-documentation/container-webpage.png)
