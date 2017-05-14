---
title: "Azure Cloud Shell（预览版）功能 | Microsoft Docs"
description: "Azure Cloud Shell 的功能概述"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: da78ec5c82a6ea46565a22fce49435d4b4d93a35
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="features--tools"></a>功能和工具
Azure Cloud Shell 是一种基于浏览器的 shell 体验，用于管理和开发 Azure 资源。

Cloud Shell 提供可通过浏览器访问的预配置 shell 体验，用于管理 Azure 资源，而无需花费自己安装计算机、对其进行版本控制和维护的开销。

Cloud Shell 按请求预配计算机，因此计算机状态不会在会话之间保留。 由于 Cloud Shell 专为交互式会话构建，因此 shell 在处于非活动状态 10 分钟后将自动终止。

## <a name="tools"></a>工具
|类别   |名称   |
|---|---|
|Linux shell 解释程序|Bash<br> sh               |
|Azure 工具            |Azure CLI 2.0 和 1.0     |
|文本编辑器           |vim<br> nano<br> emacs       |
|源代码管理         |git                    |
|生成工具            |make<br> maven<br> npm<br> pip         |
|容器             |Docker<br> Kubectl<br> DC/OS CLI         |
|数据库              |MySQL 客户端<br> PostgreSql 客户端<br> sqlcmd 实用工具      |
|其他                  |iPython 客户端 |

## <a name="language-support"></a>语言支持
|语言   |版本   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 和 3.5|

## <a name="secure-automatic-authentication"></a>安全自动身份验证
Cloud Shell 安全并自动地验证帐户对 Azure CLI 2.0 的访问权限。

## <a name="azure-files-persistence"></a>Azure 文件持久性
由于使用临时计算机按请求将 $Home 外部的本地文件分配给 Cloud Shell，因此计算机状态不会在会话之间保留。
若要在会话之间保存文件，Cloud Shell 会指导你完成在首次启动时附加 Azure 文件共享。
完成后，Cloud Shell 将自动为所有将来的会话附加存储。

[详细了解如何将 Azure 文件共享附加到 Cloud Shell](persisting-shell-storage.md)。

## <a name="next-steps"></a>后续步骤
[Cloud Shell 快速入门](quickstart.md) 
[了解 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)
