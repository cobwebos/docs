---
title: "Azure 机器学习部署故障排除指南 | Microsoft Docs"
description: "有关部署和服务创建的故障排除指南"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>模型管理故障排除
下面的信息有助于确定部署期间或调用 Web 服务时发生错误的原因。
 
## <a name="1-service-logs"></a>1.服务日志
服务 CLI 的 `logs` 选项从 Docker 和 Kubernetes 提供日志数据。

```
az ml service logs realtime -i <web service id>
```

对于其他日志设置，请使用 `--help`（或 `-h`）选项。

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2.调试和详细选项
在部署服务时使用 `--debug` 标志显示调试日志。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

在部署服务时使用 `--verbose` 标志查看其他详细信息。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3.App Insights
创建 Web 服务以启用请求级别日志记录时，将 `-l` 标志设置为 true。 在 Azure 中，会将请求日志写入环境的 App Insights 实例。 使用 `az ml env setup` 命令时，使用所用环境名称搜索此实例。

- 创建服务时，将 `-l` 设置为 true。
- 在 Azure 门户中打开 App Insights。 使用环境名称查找 App Insights 实例。
- 进入 App Insights 后，单击顶部菜单中的“搜索”以查看结果。
- 或转到 `Analytics` > `Exceptions` > `exceptions take | 10`。


## <a name="4-error-handling-in-script"></a>4.脚本中的错误处理
在 `scoring.py` 脚本的 run 函数中使用异常处理，将错误信息作为 Web 服务输出的一部分返回。

Python 示例：
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5.其他常见问题
- 如果 `env setup` 命令失败，请确保订阅中有足够的可用内核。
- 请勿在 Web 服务名称中使用下划线 (_)（如 my_webservice）。
- 如果在调用 Web 服务时收到“502 错误的网关”错误，请重试。 这通常意味着尚未将容器部署到群集。
- 如果在创建服务时收到“CrashLoopBackOff”错误，请检查日志。 这通常是由于 init 函数中缺少依赖关系。