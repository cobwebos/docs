---
title: "Azure Cloud Shell（预览版）功能 | Microsoft Docs"
description: "Azure Cloud Shell 的功能概述"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 67f03d5857e37b253ac57536e289b5468d69e9b5
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Azure Cloud Shell 的功能和工具
Azure Cloud Shell 是一种基于浏览器的 shell 体验，用于管理和开发 Azure 资源。

Cloud Shell 提供可通过浏览器访问的预配置 shell 体验，用于管理 Azure 资源，而无需花费自己安装计算机、对其进行版本控制和维护的开销。

Cloud Shell 按请求预配计算机，因此计算机状态不会在会话之间保留。 由于 Cloud Shell 专为交互式会话构建，因此 shell 在处于非活动状态 20 分钟后会自动终止。

## <a name="bash-in-cloud-shell"></a>Cloud Shell 中的 Bash
### <a name="tools"></a>工具
|类别   |名称   |
|---|---|
|Linux shell 解释程序|Bash<br> sh               |
|Azure 工具            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) 和 [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [批处理 Shipyard](https://github.com/Azure/batch-shipyard)     |
|文本编辑器           |vim<br> nano<br> emacs       |
|源代码管理         |git                    |
|生成工具            |make<br> maven<br> npm<br> pip         |
|容器             |[Docker CLI](https://github.com/docker/cli)/[Docker 计算机](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Draft](https://github.com/Azure/draft)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|数据库              |MySQL 客户端<br> PostgreSql 客户端<br> [sqlcmd 实用工具](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|其他                  |iPython 客户端<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>语言支持
|语言   |版本   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 和 3.5（默认）|

## <a name="secure-automatic-authentication"></a>安全自动身份验证
Cloud Shell 安全并自动地验证帐户对 Azure CLI 2.0 的访问权限。

## <a name="azure-files-persistence"></a>Azure 文件持久性
由于 Cloud Shell 使用临时计算机按请求进行分配，因此，$Home 外部的文件和计算机状态不会在会话之间持久保存。
要在会话之间保存文件，Cloud Shell 会指导完成在首次启动时附加 Azure 文件共享。
完成后，Cloud Shell 会自动为所有将来的会话附加存储。

[详细了解如何将 Azure 文件共享附加到 Cloud Shell](persisting-shell-storage.md)。

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md) <br>
[了解 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
