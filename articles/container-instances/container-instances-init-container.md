---
title: 运行初始化容器
description: 在 Azure 容器实例中运行 init 容器，以便在应用程序容器运行之前在容器组中执行安装任务。
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954275"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>在容器组中运行初始化容器以执行安装任务

Azure 容器实例支持容器组中的*init 容器*。 初始容器在应用程序容器或容器启动之前运行到完成。 类似于[Kubernetes init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，使用一个或多个 init 容器来执行应用容器的初始化逻辑，如设置帐户、运行安装脚本或配置数据库。

本文介绍如何使用 Azure 资源管理器模板来配置具有 init 容器的容器组。

## <a name="things-to-know"></a>使用须知

* **API 版本**-至少需要 Azure 容器实例 API 版本2019-12-01，才能部署 init 容器。 使用 `initContainers` [YAML 文件](container-instances-multi-container-yaml.md)或[资源管理器模板](container-instances-multi-container-group.md)中的属性进行部署。
* **执行顺序**-初始化容器按模板中指定的顺序执行，在其他容器之前执行。 默认情况下，最多可以为每个容器组指定 59 init 容器。 组中必须至少有一个非 init 容器。
* **主机环境**-初始化容器与组中的其余容器在同一硬件上运行。
* **资源**-不为 init 容器指定资源。 系统会向他们授予容器组可用的 Cpu 和内存总量。 当 init 容器运行时，不会在组中运行任何其他容器。
* **支持的属性**-Init 容器可以使用组属性，如卷、机密和托管标识。 但是，如果为容器组配置了端口或 IP 地址，则不能使用端口或 IP 地址。 
* **重新启动策略**-在组中的下一个容器启动之前，每个 init 容器必须成功退出。 如果初始化容器未成功退出，则其重新启动操作取决于为组配置的[重启策略](container-instances-restart-policy.md)：

    |组中的策略  |Init 中的策略  |
    |---------|---------|
    |Always     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Never     |Never         |
* **费用**-从初始容器的第一个部署中，容器组会产生费用。

## <a name="resource-manager-template-example"></a>资源管理器模板示例

首先将以下 JSON 复制到名为 `azuredeploy.json` 的新文件。 该模板将设置一个包含一个初始容器和两个应用程序容器的容器组：

* *Init1*容器从 Docker 中心运行[busybox](https://hub.docker.com/_/busybox)映像。 它休眠了60秒，然后将命令行字符串写入[emptyDir 卷](container-instances-volume-emptydir.md)中的文件。
* 这两个应用程序容器均运行 Microsoft `aci-wordcount` 容器映像：
    * *哈姆雷特*容器在其默认配置中运行 wordcount 应用程序，并计算莎士比亚的播放*哈姆雷特*中的单词频率。
    * *朱丽叶*应用容器从 emptDir 卷读取命令行字符串，以运行 wordcount 应用，而不是莎士比亚的*罗密欧和朱丽叶*。

有关使用映像的详细信息和示例 `aci-wordcount` ，请参阅[在容器实例中设置环境变量](container-instances-environment-variables.md)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>部署模板

使用“[az group create][az-group-create]”命令创建资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az deployment group create][az-deployment-group-create] 命令部署模板。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

在具有 init 容器的组中，部署时间增加，因为初始化容器或容器需要花费时间才能完成。


## <a name="view-container-logs"></a>查看容器日志

若要验证 init 容器是否成功运行，请使用[az container logs][az-container-logs]命令查看应用容器的日志输出。 `--container-name` 参数指定从中拉取日志的容器。 在此示例中，请提取*哈姆雷特*和*朱丽叶*容器的日志，其中显示了不同的命令输出：

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

输出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

输出：

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>后续步骤

Init 容器可帮助您为应用程序容器执行安装和初始化任务。 若要详细了解如何运行基于任务的容器，请参阅[使用重启策略运行容器化任务](container-instances-restart-policy.md)。

Azure 容器实例提供了其他用于修改应用程序容器行为的选项。 示例包括：

* [在容器实例中设置环境变量](container-instances-environment-variables.md)
* [在容器实例中设置命令行来替代默认的命令行操作](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
