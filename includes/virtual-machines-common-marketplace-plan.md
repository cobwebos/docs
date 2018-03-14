---
title: "include 文件"
description: "include 文件"
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
## <a name="deploy-an-image-with-marketplace-terms"></a>部署具有 Marketplace 条款的映像

Azure Marketplace 中的某些 VM 映像具有附加许可条款和购买条款，你必须接受这些条款，然后才能以编程方式部署这些映像。  

若要从此类映像部署 VM，需要接受映像的条款并启用编程部署。 只需在订阅中执行一次此操作。 然后，每次以编程方式从映像部署 VM 时，还需要指定“购买计划”参数。

以下部分介绍如何执行这些操作：

* 了解 Marketplace 映像是否具有附加许可条款 
* 以编程方式接受条款
* 以编程方式部署 VM 时提供购买计划参数

