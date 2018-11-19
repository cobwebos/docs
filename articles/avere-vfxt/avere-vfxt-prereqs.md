---
title: Avere vFXT 先决条件 - Azure
description: Avere vFXT for Azure 的先决条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: d32c664049b7e7c1231e78c552e7c61d016fbe84
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51286752"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>准备创建 Avere vFXT

本文介绍在创建 Avere vFXT 群集前必须完成的任务。

## <a name="create-a-new-subscription"></a>创建新订阅

首先创建一个新的 Azure 订阅。 为每个 Avere vFXT 项目使用单独的订阅，可以轻松跟踪所有的项目资源和费用，使其他项目在预配期间不受可能的资源限制，并简化清理过程。  

在 Azure 门户中创建新的 Azure 订阅：

* 导航到[订阅边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* 单击顶部的“+ 添加”按钮
* 如果提示，请登录
* 选择套餐，逐步完成创建新订阅的各个步骤

## <a name="configure-subscription-owner-permissions"></a>配置订阅所有者权限

具有订阅所有者权限的用户应创建 vFXT 群集。 以下操作（以及一些其他操作）需要订阅所有者权限：

* 接受 Avere vFXT 软件的条款
* 创建群集节点访问角色
* 允许群集控制器节点管理资源组和虚拟网络 
* 允许群集控制器创建和修改群集节点 

如果不希望授予所有者对创建 vFXT 的用户的访问权限，有以下两种解决方法：

* 如果满足以下条件，资源组所有者可以创建群集：

  * 订阅所有者必须[接受 Avere vFXT 软件条款](#accept-software-terms-in-advance)并[创建群集节点访问角色](avere-vfxt-deploy.md#create-the-cluster-node-access-role)。
  * 必须在资源组内部署所有 Avere vFXT 资源，包括：
    * 群集控制器
    * 群集节点
    * Blob 存储
    * 网络元素
 
* 如果创建了额外的访问角色并提前分配了给用户，则没有所有者权限的用户也可以创建 vFXT 群集。 但是，此角色为这些用户提供了重要权限。 [本文](avere-vfxt-non-owner.md)介绍如何授权非所有者创建群集。

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 群集的配额

以下 Azure 组件必须具有足够的配额。 根据需要[请求增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此处列出的虚拟机和 SSD 组件适用于 vFXT 群集本身。 需要为计划用于计算场的 VM 和 SSD 追加配额。  确保为要运行工作流的区域启用了配额。

|Azure 组件|Quota|
|----------|-----------|
|虚拟机|至少 3 个 D16s_v3 或 E32s_v3|
|高级 SSD 存储|200 GB OS 空间加上每个节点的 1 TB 到 4 TB 缓存空间 |
|存储帐户（可选） |v2|
|数据后端存储（可选） |一个新的 LRS Blob 容器 |

## <a name="accept-software-terms-in-advance"></a>提前接受软件条款

> [!NOTE] 
> 如果订阅所有者创建 Avere vFXT 群集，则不需要此步骤。

在创建群集之前，必须接受 Avere vFXT 软件的服务条款。 如果你不是订阅所有者，请让订阅所有者提前接受条款。 每个订阅只需执行一次此步骤。

提前接受软件条款： 

1. 在 Azure 门户中打开 Cloud Shell 或浏览到 <https://shell.azure.com>。 使用订阅 ID 登录。

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 发出此命令以接受服务条款并启用 Avere vFXT for Azure 软件映像的编程访问： 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>下一步：创建 vFXT 群集

完成这些任务后，就可以开始创建群集。 阅读[部署 vFXT 群集](avere-vfxt-deploy.md)，查看说明。