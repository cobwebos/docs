---
title: 在容器实例上设置活动探测
description: 了解如何配置运行情况探测以重启 Azure 容器实例中不正常的容器
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934163"
---
# <a name="configure-liveness-probes"></a>配置运行情况探测

容器化应用程序可能会长时间运行，从而导致损坏的状态，这些状态可能需要通过重新启动容器进行修复。 Azure 容器实例支持活动探测，以便在关键功能不工作时，将容器组内的容器配置为重新启动。 活动探测的行为类似于[Kubernetes 活动探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

本文介绍了如何部署包括运行情况探测的容器组，演示了模拟的不正常容器的自动重启。

Azure 容器实例还支持[准备情况探测](container-instances-readiness-probe.md)，你可以将其配置为确保流量仅在准备就绪时才到达容器。

> [!NOTE]
> 目前不能在部署到虚拟网络的容器组中使用活动探测。

## <a name="yaml-deployment"></a>YAML 部署

创建包含下面的代码片段的 `liveness-probe.yaml` 文件。 此文件定义了包含最终变得不正常的 NGNIX 容器的容器组。

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

运行以下命令来使用上面的 YAML 部署此容器组：

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>启动命令

部署包含一个 `command` 属性，该属性定义在容器首次开始运行时运行的启动命令。 此属性接受字符串数组。 此命令模拟进入不正常状态的容器。

首先，它会启动一个 bash 会话，并在 `/tmp` 目录中创建一个名为 `healthy` 的文件。 然后，在删除该文件前休眠30秒，然后进入10分钟睡眠状态：

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>运行情况命令

此部署定义一个 `livenessProbe`，该支持作为活动检查的 `exec` 活动命令。 如果此命令以非零值退出，则将终止并重新启动容器，并通知 `healthy` 文件找不到。 如果此命令成功退出，并退出代码为0，则不执行任何操作。

`periodSeconds` 属性指定运行情况命令应当每 5 秒执行一次。

## <a name="verify-liveness-output"></a>验证运行情况输出

在前 30 秒内，启动命令创建的 `healthy` 文件存在。 当活动命令检查是否存在 `healthy` 文件时，状态代码将返回0，并通知成功，因此不会重新启动。

30秒后，`cat /tmp/healthy` 命令将开始失败，从而导致发生不正常和中止的事件。

可以通过 Azure 门户或 Azure CLI 查看这些事件。

![门户不正常事件][portal-unhealthy]

通过查看 Azure 门户中的事件，可在活动命令失败的情况下触发 `Unhealthy` 类型的事件。 后续事件的类型为 `Killing`，表示删除容器，因此可以开始重新启动。 每次发生此事件时，容器的重启计数都会增加。

重新启动已完成，因此会保留公共 IP 地址和特定于节点的内容等资源。

![门户重启计数器][portal-restart]

如果活动探查连续失败并触发过多的重启，则容器将进入指数回退延迟。

## <a name="liveness-probes-and-restart-policies"></a>运行情况探测和重启策略

重启策略会取代由运行情况探测触发的重启行为。 例如，如果您设置了一个 `restartPolicy = Never`*和*一个活动探测，则由于活动检查失败，容器组将不会重新启动。 容器组应遵循 `Never`的容器组重新启动策略。

## <a name="next-steps"></a>后续步骤

基于任务的方案可能会要求运行情况探测在先决条件功能不正常工作时能够自动重启。 若要详细了解如何运行基于任务的容器，请参阅[在 Azure 容器实例中运行容器化任务](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
