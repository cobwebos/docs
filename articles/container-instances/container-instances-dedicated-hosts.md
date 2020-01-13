---
title: 在专用主机上部署
description: 使用专用主机实现工作负荷的真正主机级隔离
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903751"
---
# <a name="deploy-on-dedicated-hosts"></a>在专用主机上部署

"专用" 是 Azure 容器实例（ACI） sku，为安全运行的容器提供了一个独立的专用计算环境。 使用专用的 sku 会使每个容器组在 Azure 数据中心拥有专用的物理服务器，确保完整的工作负荷隔离，以帮助满足组织的安全性和符合性要求。 

专用 sku 适用于需要从物理服务器角度隔离工作负荷的容器工作负载。

## <a name="using-the-dedicated-sku"></a>使用专用 sku

> [!IMPORTANT]
> 仅在当前推出的最新 API 版本（2019-12-01）中提供专用 sku。在部署模板中指定此 API 版本。 此外，使用专用 sku 的任何订阅的默认限制均为0。 如果要将此 sku 用于生产容器部署，请创建[Azure 支持请求][azure-support]

从 API 版本2019-12-01 开始，部署模板的容器组属性部分下有一个 "sku" 属性，它是 ACI 部署所必需的。 目前，可以将此属性用作用于 ACI 的 Azure 资源管理器部署模板的一部分。 有关使用模板部署 ACI 资源的详细信息，请[参阅教程：使用资源管理器模板部署多容器组](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)。 

Sku 属性可以具有以下值之一：
* 标准-标准 ACI 部署选择，仍然保证虚拟机监控程序级别的安全性 
* 专用于将专用主机用于容器组的工作负荷级别隔离

## <a name="modify-your-json-deployment-template"></a>修改 JSON 部署模板

在指定了容器组资源的部署模板中，确保 `"apiVersion": "2019-12-01",`。 在容器组资源的 "属性" 部分中，将 `"sku": "Dedicated",`设置。

下面是使用专用 sku 的容器组部署模板的资源部分的示例代码段：

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
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>部署容器组

如果在桌面上创建并编辑了部署模板文件，可以通过将文件拖动到 Cloud Shell 目录上，将其上传到该文件。 

使用“az group create”命令创建资源组[][az-group-create]。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令部署模板。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

将在几秒钟内收到来自 Azure 的初始响应。 部署完成后，所有与该服务相关的数据都将使用您提供的密钥进行加密。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
