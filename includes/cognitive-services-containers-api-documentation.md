---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034429"
---
## <a name="validate-that-a-container-is-running"></a>验证容器是否正在运行 

有几种方法可用于验证容器是否正在运行。 找到相关容器的*外部 IP*地址和公开端口, 并打开你喜欢的 web 浏览器。 使用以下各种请求 Url 来验证容器是否正在运行。 下面列出的示例请求 url 为`http://localhost:5000`, 但特定容器可能会有所不同。 请记住, 你要依赖容器的*外部 IP*地址和公开的端口。

| 请求 URL | 用途 |
|--|--|
| `http://localhost:5000/` | 容器提供主页。 |
| `http://localhost:5000/status` | 使用 HTTP GET 请求, 用于验证容器是否正在运行而不会导致终结点查询。 此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。 |
| `http://localhost:5000/swagger` | 该容器提供了一套完整的端点文档和一项**试用**功能。 使用此功能可以将设置输入到基于 Web 的 HTML 表单并进行查询，而无需编写任何代码。 查询返回后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。 |

![容器的主页](./media/cognitive-services-containers-api-documentation/container-webpage.png)
