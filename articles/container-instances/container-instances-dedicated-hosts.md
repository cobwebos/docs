---
title: 在专用主机上部署
description: 使用专用主机实现 Azure 容器实例工作负荷的真正主机级隔离
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025027"
---
# <a name="deploy-on-dedicated-hosts"></a>在专用主机上部署

“专用”是指一个 Azure 容器实例 (ACI) SKU，它为安全运行的容器提供独立且专用的计算环境。 使用专用 SKU 会使每个容器组在 Azure 数据中心拥有专用的物理服务器，确保工作负载完全隔离，有助于满足组织的安全性和符合性要求。 

专用 SKU 适用于需要从物理服务器角度隔离工作负载的容器工作负载。

## <a name="prerequisites"></a>必备条件

* 使用专用 sku 的任何订阅的默认限制为0。 如果要将此 sku 用于生产容器部署，请创建[Azure 支持请求][azure-support]以增加限制。

## <a name="use-the-dedicated-sku"></a>使用专用 sku

> [!IMPORTANT]
> 仅在当前推出的最新 API 版本（2019-12-01）中提供专用 sku。在部署模板中指定此 API 版本。
>

从 API 版本2019-12-01 开始，部署模板的`sku`容器组属性部分下有一个属性，它是 ACI 部署所必需的。 目前，可以将此属性用作 ACI 的 Azure 资源管理器部署模板的一部分。 若要详细了解如何使用模板部署 ACI 资源，请[参阅教程：使用资源管理器模板部署多容器组](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)。 

`sku`属性可以具有下列值之一：
* `Standard`-标准 ACI 部署选择，仍然保证虚拟机监控程序级别的安全性 
* `Dedicated`-用于工作负荷级别与容器组的专用物理主机的隔离

## <a name="modify-your-json-deployment-template"></a>修改 JSON 部署模板

在部署模板中，修改或添加以下属性：
* 在`resources`下， `apiVersion`将`2019-12-01`设置为。
* 在容器组属性下，添加一个`sku`具有值`Dedicated`的属性。

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
