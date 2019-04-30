---
title: include 文件
description: include 文件
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880533"
---
## <a name="terminology"></a>术语

Azure 中的市场映像具有以下属性：

* **发布者**：创建映像的组织。 示例：Canonical、MicrosoftWindowsServer
* **产品/服务**：发布者创建的一组相关映像的名称。 示例：UbuntuServer、WindowsServer
* **SKU**：产品/服务的实例，例如分发版的主要版本。 示例：18.04-LTS、2019-Datacenter
* **版本**：映像 SKU 的版本号。 

若要在以编程方式部署 VM 时标识市场映像，请以参数的形式单独提供这些值。 有些工具接受映像 URN，它将这些值合并，值之间用冒号 (:) 字符隔开：发布者:产品/服务:Sku:版本。 在 URN 中，可将版本号替换为“latest”，这会选择最新的映像版本。 

如果映像发布者提供附加许可条款和购买条款，则必须接受这些条款并启用编程部署。 以编程方式部署 VM 时，还需要提供“购买计划”参数。 请参阅[部署具有市场条款的映像](#deploy-an-image-with-marketplace-terms)。

<!-- Update_Description: wording update -->