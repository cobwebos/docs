---
title: include 文件
description: include 文件
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743355"
---
## <a name="terminology"></a>术语

Azure 中的 Marketplace 映像具有以下属性：

* 发布者 - 创建映像的组织。 示例：Canonical、MicrosoftWindowsServer
* **产品/服务** - 发布者创建的一组相关映像的名称。 示例：Ubuntu Server、WindowsServer
* SKU - 产品/服务的实例，例如分发的主要版本。 示例：16.04-LTS、2016-Datacenter
* 版本 - 映像 SKU 的版本号。 

若要在以编程方式部署 VM 时标识 Marketplace 映像，请以参数的形式单独提供这些值，或者使用某些工具接受映像 *URN*。 URN 将这些值合并，值之间用冒号 (:) 字符隔开：*发布者*:*产品/服务*:*Sku*:*版本*。 在 URN 中，可将版本号替换为“latest”，这会选择最新的映像版本。 

如果映像发布者提供附加许可条款和购买条款，你必须接受这些条款并启用编程部署。 以编程方式部署 VM 时，还需要提供“购买计划”参数。 请参阅[部署具有 Marketplace 条款的映像](#deploy-an-image-with-marketplace-terms)。