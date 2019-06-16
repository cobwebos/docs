---
title: 存储和使用 Azure Service Fabric 网格应用程序机密 | Microsoft Docs
description: 存储和使用 Service Fabric 网格机密。
services: service-fabric-mesh
keywords: secrets
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 4268356db5f46e92862e19d6391cfe5a94511270
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60810661"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric 网格应用程序机密
Service Fabric 网格支持将机密作为 Azure 资源。 Service Fabric 网格机密可以是任何敏感文本信息，例如存储连接字符串、密码或应该安全存储和传输的其他值。

![网格机密概述][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>网格机密资源
网格应用程序机密包括：
* 一个**机密**资源，它是一个用于存储文本机密的容器。 **机密**资源中包含的机密将安全地进行存储和传输。
* 存储在**机密**资源容器中的一个或多个**机密/值**资源。 每个**机密/值**资源都由版本号予以区分。

## <a name="next-steps"></a>后续步骤 
若要详细了解 Service Fabric 网格机密，请参阅：
- [管理 Service Fabric 网格应用程序机密](service-fabric-mesh-howto-manage-secrets.md)
- [Service Fabric 资源模型简介](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
