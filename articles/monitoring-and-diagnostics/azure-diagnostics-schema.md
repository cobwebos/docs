---
title: "Azure 诊断配置架构版本列表 | Microsoft 文档"
description: "用于配置 Azure 虚拟机、VM 规模集、Service Fabric 和云服务中的性能计数器集合。"
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 2e1bc45b55cd79af8579a5ddaf43cf0db019c92f


---
# <a name="list-of-azure-diagnostics-versions"></a>Azure 诊断版本列表
此页对 Microsoft Azure SDK 附带的 Azure 诊断架构版本进行了索引。  

> [!NOTE]
> Azure 诊断是一个组件，用于从 Azure 虚拟机、虚拟机规模集、Service Fabric 和云服务收集性能计数器和其他统计信息。  在使用以下某个服务时，才需要参阅此页。
>

Azure 诊断可以与其他 Microsoft 诊断产品（例如 Azure Monitor、Application Insights 和 Log Analytics）结合使用。

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK 和诊断版本随附图  

|Azure SDK 版本 | Azure 诊断版本 | 模型|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | 插件|  
|2.0 - 2.4         |1.0                         |"|  
|2.5               |1.2                         |扩展|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  

 Azure 诊断 1.0 版本最初随附于插件模型中，也就是说，安装 Azure SDK 时，便可获取附带的 Azure 诊断版本。  

 从 SDK 2.5（诊断版本 1.2）开始，Azure 诊断随附于扩展模型中。 使用新功能的工具仅在较新版本的 Azure SDK 中可用，但使用诊断的任何云服务或虚拟机将直接从 Azure 选取最新的附带版本。  

 例如，对于仍使用 SDK 2.5 的用户，无论是否使用更新的功能，都将加载诊断 1.5 版。  

## <a name="azure-diagnostics-schemas-index"></a>Azure 诊断架构索引  
[诊断 1.0 配置架构](azure-diagnostics-schema-1dot0.md)  

[诊断 1.2 配置架构](azure-diagnostics-schema-1dot2.md)  

[诊断 1.3 至诊断 1.5 配置架构](azure-diagnostics-schema-1dot3-to-1dot5.md)  



<!--HONumber=Jan17_HO5-->


