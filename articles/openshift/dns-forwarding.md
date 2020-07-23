---
title: 配置 Azure Red Hat OpenShift 4 的 DNS 转发
description: 配置 Azure Red Hat OpenShift 4 的 DNS 转发
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82232626"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上配置 DNS 转发

若要在 Azure Red Hat OpenShift 群集上配置 DNS 转发，需要修改 DNS 操作员。 此修改将允许在群集内运行的应用程序盒解析群集外专用 DNS 服务器上托管的名称。 [此处](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)的 OpenShift 4.3 介绍了这些步骤。

例如，如果你想要将 *. example.com 的所有 DNS 请求转发给 DNS 服务器192.168.100.10 进行解析，则可以通过运行以下内容来编辑操作员配置：
 
```bash
oc edit dns.operator/default
```
 
这将启动一个编辑器，你可以将 `spec: {}` 其替换为：
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

保存文件并退出编辑器。

## <a name="next-steps"></a>后续步骤
在[此处](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)查看有关 OpenShift 4.3 的 DNS 转发的详细信息。