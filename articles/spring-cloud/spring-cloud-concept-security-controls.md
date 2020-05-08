---
title: 概念-Azure 春季云服务的安全控制
description: 使用内置于 Azure 春季云服务的安全控件。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594978"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure 春季云服务的安全控制
安全控制内置于 Azure 春季云服务中。

安全控制是促使 Azure 服务能够防范、检测和响应安全漏洞的一种服务质量或功能。  对于每个控件，我们使用 *"是" 或 "* *否*" 来指示它当前是否用于服务。  对于不适用于该服务的控件，我们使用了*N/A* 。 

**数据保护安全控制**

| 安全控制 | 是/否 | 注释 | 文档 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 用户上传的源和项目、配置服务器设置、应用设置和持久性存储中的数据存储在 Azure 存储中，后者会自动加密静态内容。<br><br>配置服务器缓存，在应用程序生存期内从上传的源和应用程序日志生成的运行时二进制文件将保存到 Azure 托管磁盘，该磁盘会自动加密静态内容。<br><br>从用户上传的源生成的容器映像保存在 Azure 容器注册表中，后者会自动加密静态图像内容。 | [适用于静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Azure 托管磁盘的服务器端加密](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Azure 容器注册表中的容器映像存储](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| 暂时加密 | 是 | 默认情况下，用户应用公用终结点使用 HTTPS 进行入站流量。 |  |
| 加密的 API 调用 | 是 | 通过 HTTPS 上的 Azure 资源管理器调用来配置 Azure 春季云服务的管理调用。 | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

