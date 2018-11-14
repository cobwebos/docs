---
title: 有关 Avere vFXT for Azure 的其他链接
description: 指向有关 Avere vFXT for Azure 的其他信息的链接
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958826"
---
# <a name="additional-documentation"></a>其他文档

本文包含指向有关 Avere 控制面板管理界面和相关主题的其他文档链接。 

## <a name="avere-cluster-documentation"></a>Avere 群集文档

可以在 <http://library.averesystems.com/> 的网站上找到其他 Avere 群集文档。 这些文档可帮助你了解群集的功能及其设置的配置方法。 

* [FXT 群集创建指南](<http://library.averesystems.com/#fxt_cluster>)专为由物理硬件节点组成的群集而设计，但文档中的某些信息也与 vFXT 群集相关。 具体而言，新 vFXT 群集管理员可受益于阅读这些部分：
  * [自定义支持和监视设置](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>)介绍如何自定义支持上传设置并启用远程监视。 
  * [配置 VServers 和全局命名空间](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>)具有创建面向客户端的命名空间的信息。
  * [为 Avere 群集配置 DNS](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) 介绍如何配置轮询 DNS。
  * [添加后端存储](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>)说明了如何添加核心文件管理器。

* [群集配置指南](<http://library.averesystems.com/#operations>)是 Avere 群集的设置和选项的完整参考。 vFXT 群集使用这些选项的子集，但大多数相同的配置页面都适用。

* [仪表板指南](<http://library.averesystems.com/#operations>)介绍如何使用 Avere 控制面板的群集监视功能。

## <a name="vfxt-creation-and-management-documentation"></a>vFXT 的创建和管理文档

GitHub 上提供了使用 vfxt.py（云群集的创建和管理实用工具）的完整指南：[使用 vfxt.py 管理云群集](https://github.com/Azure/AvereSDK/blob/master/docs/README.md)。  
