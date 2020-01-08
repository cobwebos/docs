---
title: 获取容器实例日志 & 事件
description: 了解如何在 Azure 容器实例中检索容器日志和事件以帮助排查容器问题
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: fe30ab875aa6cd7f465ffe69672a771e18134e1c
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2020
ms.locfileid: "75664739"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>在 Azure 容器实例中检索容器日志和事件

如果 Azure 容器实例中有错误的容器，请首先使用[az 容器日志][az-container-logs]查看其日志，并通过[az container attach 将][az-container-attach]其标准输出和标准错误流式传输。 你还可以在 Azure 门户中查看容器实例的日志和事件，或将容器组的日志和事件数据发送到[Azure Monitor 日志](container-instances-log-analytics.md)。

## <a name="view-logs"></a>查看日志

要查看容器内应用程序代码的日志，可使用 [az container logs][az-container-logs] 命令。

下面是在使用命令行重写提供无效 URL 之后，在[容器实例中设置命令行](container-instances-start-command.md#azure-cli-example)中基于示例任务的容器的日志输出：

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>附加输出流

[Az container attach][az-container-attach]命令在容器启动过程中提供诊断信息。 启动容器后，它会将 STDOUT 和 STDERR 流式传输到本地控制台。

例如，以下是在提供要处理的大型文本文件的有效 URL 之后，在[容器实例中设置命令行](container-instances-start-command.md#azure-cli-example)的基于任务的容器的输出：

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>获取诊断事件

如果容器未能成功部署，请查看 Azure 容器实例资源提供程序提供的诊断信息。 若要查看容器的事件，请运行[az container show][az-container-show]命令：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

输出包括容器的核心属性，以及部署事件（此处截断显示）：

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>后续步骤
了解如何为 Azure 容器实例[排查常见容器和部署问题](container-instances-troubleshooting.md)。

了解如何将容器组的日志和事件数据发送到[Azure Monitor 日志](container-instances-log-analytics.md)。

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show