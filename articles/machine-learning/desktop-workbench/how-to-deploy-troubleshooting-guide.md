---
title: Azure 机器学习部署故障排除指南 | Microsoft Docs
description: 有关部署和服务创建的故障排除指南
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 7b8d2f4fb52c6d4faed8e813779e3b01d1817355
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>服务部署和环境设置的故障排除
以下信息可帮助确定在设置模型管理环境时出错的原因。

## <a name="model-management-environment"></a>模型管理环境
### <a name="contributor-permission-required"></a>需要参与者权限
必须拥有对订阅或资源组的参与者访问权限，才能设置群集以便部署 Web 服务。

### <a name="resource-availability"></a>资源可用性
需要在订阅中具有足够的可用资源，以便可以预配环境资源。

### <a name="subscription-caps"></a>订阅上限
订阅可能有计费上限，这会阻止你预配环境资源。 取消上限可实现预配。

### <a name="enable-debug-and-verbose-options"></a>启用调试和详细选项
在设置命令中使用 `--debug` 和 `--verbose` 标志可在预配环境时显示调试和跟踪信息。

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>服务部署
下面的信息有助于确定部署期间或调用 Web 服务时发生错误的原因。

### <a name="service-logs"></a>服务日志
服务 CLI 的 `logs` 选项从 Docker 和 Kubernetes 提供日志数据。

```
az ml service logs realtime -i <web service id>
```

对于其他日志设置，请使用 `--help`（或 `-h`）选项。

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>调试和详细选项
在部署服务时使用 `--debug` 标志显示调试日志。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

在部署服务时使用 `--verbose` 标志查看其他详细信息。

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>在 App Insights 中启用请求日志记录
创建 Web 服务以启用请求级别日志记录时，将 `-l` 标志设置为 true。 在 Azure 中，会将请求日志写入环境的 App Insights 实例。 使用 `az ml env setup` 命令时，使用所用环境名称搜索此实例。

- 创建服务时，将 `-l` 设置为 true。
- 在 Azure 门户中打开 App Insights。 使用环境名称查找 App Insights 实例。
- 进入 App Insights 后，单击顶部菜单中的“搜索”以查看结果。
- 或转到 `Analytics` > `Exceptions` > `exceptions take | 10`。


### <a name="add-error-handling-in-scoring-script"></a>在评分脚本中添加错误处理
在 `scoring.py` 脚本的 run 函数中使用异常处理，将错误信息作为 Web 服务输出的一部分返回。

Python 示例：
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>其他常见问题
- 如果无法在 Windows 计算机上执行 azure-cli-ml 的 pip 安装，并看到错误 `cannot find the path specified`，需要启用长路径支持。 请参阅 https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/。 
- 如果 `env setup` 命令失败，并出现 `LocationNotAvailableForResourceType`，可能是因为机器学习资源位置（区域）错误。 请确保使用 `-l` 参数指定的位置是 `eastus2`、`westcentralus` 或 `australiaeast`。
- 如果 `env setup` 命令失败，并出现 `Resource quota limit exceeded`，请确保订阅中有足够的内核，且资源未在其他进程中耗尽。
- 如果 `env setup` 命令失败，并出现 `Invalid environment name. Name must only contain lowercase alphanumeric characters`，请确保服务名称中不包含大写字母、符号或下划线 (_)（如 my_environment 所示）。
- 如果 `service create` 命令失败，并出现 `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`，请确保服务名称在 3 到 32 个字符之间；以小写字母数字字符开头和结尾；且不包含大写字母，以及除连字符 (-) 和句点 (. )，或下划线 (_) 以外的其他符号（如 my_webservice 所示）。
- 如果在调用 Web 服务时出现 `502 Bad Gateway` 错误，请重试。 这通常意味着尚未将容器部署到群集。
- 如果在创建服务时出现 `CrashLoopBackOff` 错误，请检查日志。 这通常是由于 init 函数中缺少依赖关系。
