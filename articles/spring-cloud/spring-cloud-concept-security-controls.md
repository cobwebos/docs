---
title: 概念-Azure 春季云服务的安全控制
description: 使用内置于 Azure 春季云服务的安全控件。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2e001e5e927d9d4c5dc4c3eb74f7b5ad33617b99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037570"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud 服务的安全控制
安全控制内置于 Azure 春季云服务中。

安全控制是促使 Azure 服务能够防范、检测和响应安全漏洞的一种服务质量或功能。  对于每个控件，我们使用 *"是" 或 "* *否*" 来指示它当前是否用于服务。  对于不适用于该服务的控件，我们使用了*N/A* 。 

**数据保护安全控制**

| 安全控制 | Yes/No | 注释 | 文档 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 用户上传的源和项目、配置服务器设置、应用设置和持久性存储中的数据存储在 Azure 存储中，后者会自动加密静态内容。<br><br>配置服务器缓存，在应用程序生存期内从上传的源和应用程序日志生成的运行时二进制文件将保存到 Azure 托管磁盘，该磁盘会自动加密静态内容。<br><br>从用户上传的源生成的容器映像保存在 Azure 容器注册表中，后者会自动加密静态图像内容。 | [静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Azure 托管磁盘的服务器端加密](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Azure 容器注册表中的容器映像存储](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| 暂时加密 | 是 | 默认情况下，用户应用公用终结点使用 HTTPS 进行入站流量。 |  |
| 加密的 API 调用 | 是 | 通过 HTTPS 上的 Azure 资源管理器调用来配置 Azure 春季云服务的管理调用。 | [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/) |

**网络访问安全控制**

| 安全控制 | Yes/No | 注释 | 文档 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 服务标记 | 是 | 使用**AzureSpringCloud**服务标记来定义[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)或[azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)上的出站网络访问控制，以允许到 Azure 春季云应用程序的流量。<br><br>*注意：* 目前，2020/07/14 之后创建的新的 Azure 春季云服务实例支持**AzureSpringCloud**服务标记。 | [服务标记](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |
