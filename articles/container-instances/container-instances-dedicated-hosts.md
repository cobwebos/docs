---
title: 部署在专用主机上
description: 使用专用主机实现 Azure 容器实例工作负载的真正主机级隔离
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025027"
---
# <a name="deploy-on-dedicated-hosts"></a>在专用主机上部署

“专用”是指一个 Azure 容器实例 (ACI) SKU，它为安全运行的容器提供独立且专用的计算环境。 使用专用 SKU 会使每个容器组在 Azure 数据中心拥有专用的物理服务器，确保工作负载完全隔离，有助于满足组织的安全性和符合性要求。 

专用 SKU 适用于需要从物理服务器角度隔离工作负载的容器工作负载。

## <a name="prerequisites"></a>先决条件

* 任何订阅使用专用 sKU 的默认限制为 0。 如果要将此 sKU 用于生产容器部署，请创建[Azure 支持请求][azure-support]以增加限制。

## <a name="use-the-dedicated-sku"></a>使用专用 sKU

> [!IMPORTANT]
> 使用专用 sKU 仅在当前推出的最新 API 版本 （2019-12-01） 中可用。在部署模板中指定此 API 版本。
>

从 API 版本 2019-12-01 开始`sku`，部署模板的容器组属性部分下有一个属性，这是 ACI 部署所必需的。 目前，可以将此属性用作 ACI 的 Azure 资源管理器部署模板的一部分。 了解有关使用教程中的模板部署 ACI 资源的更多信息[：使用资源管理器模板部署多容器组](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)。 

该`sku`属性可以具有以下值之一：
* `Standard`- 标准 ACI 部署选择，仍保证虚拟机管理程序级安全性 
* `Dedicated`- 用于与容器组的专用物理主机进行工作负载级隔离

## <a name="modify-your-json-deployment-template"></a>修改 JSON 部署模板

在部署模板中，修改或添加以下属性：
* 在`resources`下`apiVersion`，`2019-12-01`设置为 。
* 在容器组属性下，添加`sku`值 的属性`Dedicated`。

下面是使用专用 SKU 的容器组部署模板的 resources 节的示例代码片段：

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

下面是部署运行单个容器实例的示例容器组的完整模板：

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
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>部署容器组

如果在桌面上创建并编辑了部署模板文件，则可以通过将文件拖入其中将其上载到云外壳目录。 

使用“[az group create][az-group-create]”命令创建资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令部署模板。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

将在几秒钟内收到来自 Azure 的初始响应。 成功部署将在专用主机上进行。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
