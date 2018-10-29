---
title: 在 Azure 市场中创建虚拟机产品/服务 | Microsoft Docs
description: 列出了为 Azure 市场创建新的虚拟机 (VM) 产品/服务所需执行的步骤。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 0a875b69a10f6305b4ffe32b63d3db5b508faf01
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639027"
---
# <a name="create-virtual-machine-offer"></a>创建虚拟机产品/服务

本部分列出了为 Azure 市场创建新的虚拟机 (VM) 产品/服务请求所需执行的步骤。  每个产品/服务在 Azure 市场中都显示为其自己的实体并且与一个或多个 SKU 相关联。  VM 产品/服务由以下资产组和支持服务组成： 

![VM 产品/服务的资产](./media/publishvm_002.png)

其中：

|  **资产组**   |  **说明**  |
|  ---------------   |  ---------------  |
|    SKU            |  产品/服务的最小可购买单位。 单个产品/服务（产品分类）可以具有多个与之关联的 SKU，以便区分所支持的功能、VM 映像类型和计费模型。 |
|  市场       | 包含市场营销、法律以及潜在顾客管理资产和规范。  <ul><li> 市场营销资产包括产品/服务名称、说明和徽标</li> <li> 法律资产包括隐私政策、使用条款和其他法律文档</li>  <li> 潜在顾客管理政策允许你指定如何处理来自 Azure 市场最终用户门户的潜在顾客。</li> </ul> |
| 支持            | 包含支持部门联系方式和政策信息 |
| 体验版         | 定义最终用户在购买之前可以用来测试你的产品/服务的资产 |
|  |  |


## <a name="new-offer-form"></a>“新建产品/服务”窗体

登录到[云合作伙伴门户](http://cloudpartner.azure.com/)后，在左侧菜单栏上单击“+ 新建产品/服务”项。 在结果菜单中，单击“虚拟机”以显示“新建产品/服务”窗体，并启动为新的 VM 产品/服务定义资产的过程。 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![新建虚拟机产品/服务用户界面选择](./media/publishvm_003.png)

> [!WARNING]
> 如果“虚拟机”选项未显示或未启用，则表明你的帐户没有权限创建此产品/服务类型。  请检查是否已满足此产品/服务类型的所有[先决条件](./cpp-prerequisites.md)，包括开发人员帐户注册。


## <a name="next-steps"></a>后续步骤

本部分中的后续主题提供了“新建产品/服务”页面（对于 VM 产品/服务类型）中的选项卡的镜像。  每篇文章都介绍了如何使用关联的选项卡来为新的 VM 产品/服务定义资产组和支持服务。

- [“产品/服务设置”选项卡](./cpp-offer-settings-tab.md)
- [“SKU”选项卡](./cpp-skus-tab.md)
- [“体验版”选项卡](./cpp-test-drive-tab.md)
- [“市场”选项卡](./cpp-marketplace-tab.md)
- [“支持”选项卡](./cpp-support-tab.md)
