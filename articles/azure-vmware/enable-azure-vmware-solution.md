---
title: 如何启用 Azure VMware 解决方案资源
description: 了解如何提交支持请求以启用 Azure VMware 解决方案资源。 你还可以在现有的 Azure VMware 解决方案私有云中请求更多节点。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90817890"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>如何启用 Azure VMware 解决方案资源
了解如何提交支持请求以启用 Azure VMware 解决方案资源。 你还可以在现有的 Azure VMware 解决方案私有云中请求更多节点。

## <a name="eligibility-criteria"></a>资格条件

* 你需要与 Microsoft [ (EA) 的 Azure 企业协议 ](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) 。
* 需要 Azure 订阅中的 Azure 帐户。


## <a name="enable-azure-vmware-solution-resource"></a>启用 Azure VMware 解决方案资源
在创建 Azure VMware 解决方案资源之前，需要提交支持票证，以分配节点。 支持团队收到请求后，最多需要五个工作日内确认请求并分配节点。 如果你有现有的 Azure VMware 解决方案私有云，并且需要分配更多的节点，则会经历相同的过程。


1. 在 Azure 门户中，在 " **帮助 + 支持**" 下创建 **[新的支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，并为票证提供以下信息：
   - **问题类型：** 技术方面
   - **订阅：** 选择你的订阅
   - **服务：** 所有服务 > Azure VMware 解决方案
   - **资源：** 一般问题 
   - **摘要：** 需要容量
   - **问题类型：** 容量管理问题
   - **问题子类型：** 客户请求额外的主机配额/容量

1. 在支持票证 **说明** 的 " **详细信息** " 选项卡上，提供：

   - POC 或生产 
   - 区域名称
   - 节点数
   - 任何其他详细信息

   >[!NOTE]
   >Azure VMware 解决方案建议至少使用三个节点来启动私有云和冗余的 N + 1 节点。 

1. 选择 " **查看 + 创建** " 以提交请求。

   支持代表需要5个工作日内确认你的请求。

   >[!IMPORTANT] 
   >如果你已有一个现有的 Azure VMware 解决方案，但你请求的是其他节点，请注意，我们需要5个工作日来分配节点。 

1. 预配节点之前，请确保在 Azure 门户中注册了 **MICROSOFT AVS** 资源提供程序。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   有关注册资源提供程序的其他方式，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。