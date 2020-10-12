---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108889"
---
## <a name="validate-that-a-container-is-running"></a>验证容器是否正在运行 

有几种方法可用于验证容器是否正在运行。 找到相关容器的*外部 IP* 地址和公开端口，并打开你常用的 Web 浏览器。 使用以下各种请求 URL 验证容器是否正在运行。 下面列出的示例请求 URL 是 `http://localhost:5000`，但是你的特定容器可能会有所不同。 请记住，你要依赖于容器的*外部 IP* 地址和公开端口。

| 请求 URL | 目的 |
|--|--|
| `http://localhost:5000/` | 容器提供主页。 |
| `http://localhost:5000/ready` | 通过 GET 请求，这提供了一种验证，指示容器已准备好接受针对模型的查询。  此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。 |
| `http://localhost:5000/status` | 还请求了 GET，这将验证用于启动容器的 api 密钥是否有效，且不会导致终结点查询。 此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。 |
| `http://localhost:5000/swagger` | 容器为终结点提供一组完整的文档以及“尝试”功能。 使用此功能可以将设置输入到基于 Web 的 HTML 表单并进行查询，而无需编写任何代码。 查询返回后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。 |

![容器的主页](./media/cognitive-services-containers-api-documentation/container-webpage.png)
