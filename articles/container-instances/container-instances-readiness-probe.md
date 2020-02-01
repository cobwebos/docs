---
title: 在容器实例上设置就绪探测器
description: 了解如何配置探测，以确保 Azure 容器实例中的容器仅在准备就绪时接收请求
ms.topic: article
ms.date: 10/17/2019
ms.openlocfilehash: 50cb341788434a6dc0bb0a1423d9e59a3d93634d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901857"
---
# <a name="configure-readiness-probes"></a>配置就绪情况探测

对于提供流量的容器化应用程序，你可能想要验证容器是否已准备好处理传入的请求。 Azure 容器实例支持包括配置的准备情况探测，以便在某些情况下无法访问容器。 就绪探测器的行为类似于[Kubernetes 的准备情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)。 例如，容器应用可能需要在启动过程中加载大型数据集，并且你不希望在这段时间内接收请求。

本文介绍如何部署包含准备情况探测器的容器组，以便在探测成功时，容器只接收流量。

Azure 容器实例还支持[活动探测](container-instances-liveness-probe.md)，你可以将其配置为导致不正常的容器自动重启。

> [!NOTE]
> 目前不能在部署到虚拟网络的容器组中使用准备情况探测。

## <a name="yaml-configuration"></a>YAML 配置

例如，使用以下代码片段创建包含准备情况探测的 `readiness-probe.yaml` 文件。 此文件定义一个容器组，其中包含运行小型 web 应用的容器。 应用是从公共 `mcr.microsoft.com/azuredocs/aci-helloworld` 映像部署的。 此容器应用还在快速入门中进行演示，如[使用 Azure CLI 在 Azure 中部署容器实例](container-instances-quickstart.md)。

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>启动命令

YAML 文件包含一个启动命令，该命令将在容器启动时运行，由接受字符串数组的 `command` 属性定义。 此命令模拟 web 应用运行的时间，但容器未就绪。 首先，它启动 shell 会话并运行 `node` 命令来启动 web 应用。 它还启动一个命令，使其睡眠240秒，超过此时间后，将在 `/tmp` 目录中创建一个名为 `ready` 的文件：

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>准备情况命令

此 YAML 文件定义一个 `readinessProbe`，它支持可充当就绪状态检查的 `exec` 准备情况命令。 此示例准备情况命令测试 `/tmp` 目录中是否存在 `ready` 文件。

如果 `ready` 文件不存在，则准备情况命令将以非零值退出;容器将继续运行，但无法访问。 当命令成功退出并退出代码为0时，容器就可以访问了。 

`periodSeconds` 属性指定准备命令每5秒执行一次。 准备情况探测在容器组的生存期内运行。

## <a name="example-deployment"></a>示例部署

运行以下命令以部署具有上述 YAML 配置的容器组：

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>查看就绪状态检查

在此示例中，在第一个240秒内，准备情况命令在检查 `ready` 文件是否存在时失败。 状态代码返回了容器未就绪的信号。

可以通过 Azure 门户或 Azure CLI 查看这些事件。 例如，门户会显示 "就绪" 命令失败时触发的类型 `Unhealthy` 事件。 

![门户不正常事件][portal-unhealthy]

## <a name="verify-container-readiness"></a>验证容器准备情况

启动容器后，可以验证其最初是否不可访问。 预配后，获取容器组的 IP 地址：

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

尝试在准备情况探测失败时访问该站点：

```bash
wget <ipAddress>
```

输出显示，最初无法访问站点：
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240秒后，准备情况命令成功，通知容器已就绪。 现在，运行 `wget` 命令时，该命令将成功：

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

容器准备就绪后，还可以通过使用 web 浏览器浏览到 IP 地址来访问 web 应用。

> [!NOTE]
> 对于容器组的生存期，就绪状态探测将继续运行。 如果准备情况命令稍后失败，则将无法访问该容器。 
> 

## <a name="next-steps"></a>后续步骤

对于涉及包含依赖容器的多容器组的方案，准备情况探测可能很有用。 有关多容器方案的详细信息，请参阅[Azure 容器实例中的容器组](container-instances-container-groups.md)。

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
