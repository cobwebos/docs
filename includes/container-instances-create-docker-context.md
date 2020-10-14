---
title: include 文件
description: include 文件
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708240"
---
## <a name="create-azure-context"></a>创建 Azure 上下文

若要使用 Docker 命令在 Azure 容器实例中运行容器，请先登录到 Azure：

```bash
docker login azure
```

在系统提示时输入或选择 Azure 凭据。


通过运行 `docker context create aci` 创建 ACI 上下文。 此上下文将 Docker 与 Azure 订阅和资源组相关联，以便你可以创建和管理容器实例。 例如，创建名为 myacicontext 的上下文：

```
docker context create aci myacicontext
```

出现提示时，选择你的 Azure 订阅 ID，然后选择现有资源组或“创建新的资源组”。 如果选择新的资源组，则创建的新资源组将使用系统生成的名称。 Azure 容器实例（例如所有 Azure 资源）都必须部署到资源组中。 使用资源组可以组织和管理相关的 Azure 资源。


运行 `docker context ls` 以确认已将 ACI 上下文添加到 Docker 上下文：

```
docker context ls
```