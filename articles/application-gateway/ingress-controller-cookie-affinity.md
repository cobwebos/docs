---
title: 使用应用程序网关启用基于 cookie 的相关性
description: 本文提供了有关如何使用应用程序网关启用基于 cookie 的关联的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513570"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>使用应用程序网关启用基于 Cookie 的相关性
如[Azure 应用程序网关文档](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)中所述，应用程序网关支持基于 cookie 的关联，这意味着，它可以将来自用户会话的后续流量定向到相同的服务器进行处理。

## <a name="example"></a>示例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
