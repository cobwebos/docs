---
title: 在专用主机上部署
description: 使用专用主机实现 Azure 容器实例工作负荷的真正主机级隔离
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934143"
---
# <a name="deploy-on-dedicated-hosts"></a>在专用主机上部署

"专用" 是 Azure 容器实例（ACI） sku，为安全运行的容器提供了一个独立的专用计算环境。 使用专用的 sku 会使每个容器组在 Azure 数据中心拥有专用的物理服务器，确保完整的工作负荷隔离，以帮助满足组织的安全性和符合性要求。 

专用 sku 适用于需要从物理服务器角度隔离工作负荷的容器工作负载。

## <a name="prerequisites"></a>必备组件

* 使用专用 sku 的任何订阅的默认限制为0。 如果要将此 sku 用于生产容器部署，请创建[Azure 支持请求][azure-support]以增加限制。

## <a name="use-the-dedicated-sku"></a>使用专用 sku

> [!IMPORTANT]
> 仅在当前推出的最新 API 版本（2019-12-01）中提供专用 sku。在部署模板中指定此 API 版本。
>

从 API 版本2019-12-01 开始，部署模板的容器组属性部分下有一个 `sku` 属性，它是 ACI 部署所必需的。 目前，可以将此属性用作用于 ACI 的 Azure 资源管理器部署模板的一部分。 若要详细了解如何使用模板部署 ACI 资源，请[参阅教程：使用资源管理器模板部署多容器组](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)。 

`sku` 属性可以具有下列值之一：
* `Standard`-标准 ACI 部署选择，仍然保证虚拟机监控程序级别的安全性 
* `Dedicated`-用于工作负荷级别与容器组的专用物理主机的隔离

## <a name="modify-your-json-deployment-template"></a>修改 JSON 部署模板

在部署模板中，修改或添加以下属性：
* 在 "`resources`" 下，将 `apiVersion` 设置为 "`2012-12-01`"。
* 在容器组属性下，添加值为 `Dedicated`的 `sku` 属性。

下面是使用专用 sku 的容器组部署模板的资源部分的示例代码段：

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

下面是一个完整的模板，用于部署运行单个容器实例的示例容器组：

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

如果在桌面上创建并编辑了部署模板文件，可以通过将文件拖动到 Cloud Shell 目录上，将其上传到该文件。 

使用“[az group create][az-group-create]”命令创建资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令部署模板。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

将在几秒钟内收到来自 Azure 的初始响应。 在专用主机上进行成功的部署。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
