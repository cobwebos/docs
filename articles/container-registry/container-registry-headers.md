---
title: "Azure 容器注册表存储库 | Microsoft Docs"
description: "如何使用 Azure 容器注册表存储库存储 Docker 映像"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: dd4feff057269ed7106990bb63eed7fcffa2dbec
ms.contentlocale: zh-cn
ms.lasthandoff: 06/10/2017


---
# <a name="azure-container-registry-repositories"></a>Azure 容器注册表存储库

Azure 容器注册表与许多服务及 Orchestrator 兼容。 为了更方便地跟踪在其中使用 ACR 的源服务和代理，我们已经开始在 Docker.config 文件中使用 Docker 标头字段。



## <a name="viewing-repositories-in-the-portal"></a>在门户中查看存储库

ACR 标头遵循以下格式：
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Cloud：Azure、Azure Stack，或政府或特定国家/地区其他 Azure 云。 尽管当前不支持 Azure Stack 和政府云，但此参数可实现将来支持。
* Service：服务名称。
* Optionalservicename：带子服务的服务的可选参数，或用于指定 SKU（例如：与 Azure/app-service/web-apps 对应的 Web 应用）。

建议合作伙伴服务和 Orchestrator 使用特定标头值来帮助我们使用遥测功能。 如果需要，用户还可以修改传递给标头的值。

我们希望 ACR 合作伙伴使用以下值填充“X-Meta-Source-Client”字段：

| 服务名称              | 标头                                |
| ------------------------- | ------------------------------------- |
| Azure 容器服务   | azure/compute/azure-container-service |
| 应用服务 - Web 应用    | azure/app-service/web-apps            |
| 应用服务 - 逻辑应用  | azure/app-service/logic-apps          |
| 批处理                     | azure/compute/batch                   |
| 云控制台             | azure/cloud-console                   |
| 函数                 | azure/compute/functions               |
| 物联网 - 中心  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| 机器学习          | azure/data/machile-learning           |
| Service Fabric            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>后续步骤
[详细了解注册表和受支持服务以及 Orchestrator](container-registry-intro.md)

