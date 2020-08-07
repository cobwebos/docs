---
title: 如何在 Windows 容器中运行自承载 Integration Runtime
description: 了解如何在 Windows 容器中运行自承载 Integration Runtime。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927551"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>如何在 Windows 容器中运行自承载 Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

本文将介绍如何在 Windows 容器中运行自承载 Integration Runtime。
Azure 数据工厂提供自承载 Integration Runtime 的官方 windows 容器支持。 您可以下载 docker build 源代码，并在自己的持续交付管道中合并构建和运行过程。 

## <a name="prerequisites"></a>先决条件 
- [Windows 容器要求](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker 版本2.3 及更高版本 
- 自承载 Integration Runtime 4.11.7512.1 和更高版本 
## <a name="get-started"></a>入门 
1.  安装 Docker 并启用 Windows 容器 
2.  从 https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container 下载源代码
3.  下载 "SHIR" 文件夹中的最新版本 SHIR 
4.  在 shell 中打开文件夹： 
```console
cd "yourFolderPath"
```

5.  构建 windows docker 映像： 
```console
docker build . -t "yourDockerImageName" 
```
6.  运行 docker 容器： 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> 对于此命令，AUTH_KEY 是必需的。 NODE_NAME，ENABLE_HA 和 HA_PORT 是可选的。 如果未设置此值，则该命令将使用默认值。 ENABLE_HA 的默认值为 false，HA_PORT 为8060。

## <a name="container-health-check"></a>容器运行状况检查 
在120秒启动期限后，运行状况检查器每30秒定期运行一次。 它将向容器引擎提供 IR 运行状态。 

## <a name="limitations"></a>限制
目前，在 Windows 容器中运行自承载 Integration Runtime 时，我们不支持以下功能：
- HTTP 代理 
- 加密节点-与 TLS/SSL 证书的节点通信 
- 生成和导入备份 
- 后台程序服务 
- 自动更新 

### <a name="next-steps"></a>后续步骤
- 查看 [Azure 数据工厂中的集成运行时概念](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)。
- 了解如何[在 Azure 门户中创建自承载集成运行时](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)。


