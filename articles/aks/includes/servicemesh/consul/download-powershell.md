---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 6b94d892410bd665bdcb76fc96bc1602d09cf652
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530558"
---
在 Windows 上基于 PowerShell 的 shell 中，使用 `Invoke-WebRequest` 下载 Consul Helm 图表版本，并按如下所示提取 `Expand-Archive`：

```powershell
# Specify the Consul Helm chart version that will be leveraged throughout these instructions
$CONSUL_HELM_VERSION="0.10.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/hashicorp/consul-helm/archive/v$CONSUL_HELM_VERSION.zip" -OutFile "consul-helm-$CONSUL_HELM_VERSION.zip"
Expand-Archive -Path "consul-helm-$CONSUL_HELM_VERSION.zip" -DestinationPath .
Move-Item -Path consul-helm-$CONSUL_HELM_VERSION -Destination consul-helm
```

