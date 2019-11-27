---
title: 在容器实例上设置活动探测
description: 了解如何配置运行情况探测以重启 Azure 容器实例中不正常的容器
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 96d98d18a3f0ac666fb2c057216f7844b176d177
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481676"
---
# <a name="configure-liveness-probes"></a>配置运行情况探测

容器化应用程序可能会长时间运行，从而导致损坏的状态，这些状态可能需要通过重新启动容器进行修复。 Azure 容器实例支持活动探测，以便在关键功能不工作时，将容器组内的容器配置为重新启动。 活动探测的行为类似于[Kubernetes 活动探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。

本文介绍了如何部署包括运行情况探测的容器组，演示了模拟的不正常容器的自动重启。

Azure 容器实例还支持[准备情况探测](container-instances-readiness-probe.md)，你可以将其配置为确保流量仅在准备就绪时才到达容器。

## <a name="yaml-deployment"></a>YAML 部署

运行下面的代码片段，创建 `liveness-probe.yaml` 文件。 此文件定义了包含最终变得不正常的 NGNIX 容器的容器组。

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

部署定义一个启动命令，该命令将在容器第一次开始运行时运行，由 `command` 属性定义，该属性接受字符串数组。 在此示例中，它将通过传递以下命令启动 bash 会话并在 `healthy` 目录中创建名为 `/tmp` 的文件：

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 然后，它将休眠30秒，然后再删除该文件，然后进入10分钟睡眠状态。

### <a name="liveness-command"></a>运行情况命令

此部署定义一个 `livenessProbe`，该支持作为活动检查的 `exec` 活动命令。 如果此命令以非零值退出，则容器将被终止并重启，指明找不到 `healthy` 文件。 如果此命令以退出代码 0 成功退出，则不会采取任何操作。

`periodSeconds` 属性指定运行情况命令应当每 5 秒执行一次。

## <a name="verify-liveness-output"></a>验证运行情况输出

在前 30 秒内，启动命令创建的 `healthy` 文件存在。 当运行情况命令检查 `healthy` 文件是否存在时，状态代码返回零，表示成功，因此不会重启。

在 30 秒后，`cat /tmp/healthy` 将开始失败，导致不正常的和终止事件发生。

可以通过 Azure 门户或 Azure CLI 查看这些事件。

![门户不正常事件][portal-unhealthy]

通过在 Azure 门户中查看事件，在运行情况命令失败时将触发 `Unhealthy` 类型的事件。 后续事件将是 `Killing` 类型的，表示容器已删除，因此可以开始重启。 每次发生此事件时，容器的重启计数都会增加。

重启是就地完成的，因此，诸如公共 IP 地址和节点特定的内容都将保留。

![门户重启计数器][portal-restart]

如果运行情况探测连续失败，并且触发了太多次重启，则容器将进入指数后退延迟。

## <a name="liveness-probes-and-restart-policies"></a>运行情况探测和重启策略

重启策略会取代由运行情况探测触发的重启行为。 例如，如果您设置了一个 `restartPolicy = Never`*和*一个活动探测，则由于活动检查失败，容器组将不会重新启动。 容器组将改为遵守容器组的重启策略 `Never`。

## <a name="next-steps"></a>后续步骤

基于任务的方案可能会要求运行情况探测在先决条件功能不正常工作时能够自动重启。 若要详细了解如何运行基于任务的容器，请参阅[在 Azure 容器实例中运行容器化任务](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
