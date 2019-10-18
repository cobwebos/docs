---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b1f3ea33694440a9366a64dd8d778b934a1d25ff
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530571"
---
若要删除 CRDs，请运行以下命令：

```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

若要删除机密，请运行以下命令：

```bash
kubectl get secret --all-namespaces -o json | jq '.items[].metadata | ["kubectl delete secret -n", .namespace, .name] | join(" ")' -r | fgrep "istio." | xargs -t0 bash -c
```