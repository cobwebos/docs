---
title: 教程 - 使用 Azure Data Share 预览版接受和接收数据
description: 教程 - 使用 Azure Data Share 预览版接受和接收数据
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: fc63c1a0b3b496de8e5ecea58f79f1db9d872e80
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838425"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>教程：使用 Azure Data Share 预览版接受和接收数据

本教程介绍如何使用 Azure Data Share 预览版接受数据共享邀请。 另外还介绍如何接收共享给你的数据，以及如何启用定期刷新时间间隔，确保共享给你的数据快照始终是最新的。 

> [!div class="checklist"]
> * 如何接受 Azure Data Share 预览版邀请
> * 创建 Azure Data Share 预览版帐户
> * 指定数据目标
> * 创建按计划刷新的数据共享的订阅

## <a name="prerequisites"></a>先决条件
在接受数据共享邀请之前，必须预配多个 Azure 资源，这些资源已在下面列出。 

确保在接受数据共享邀请之前已满足所有先决条件。 

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
* Data Share 邀请：Microsoft Azure 发出的邀请，主题名为“来自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀请”。
* 向存储帐户添加角色分配的权限，该权限存在于 *Microsoft.Authorization/role assignments/write* 权限中。 所有者角色中存在此权限。 
* Microsoft.DataShare 的资源提供程序注册。 有关如何执行此操作的信息，请参阅 [Azure 资源提供程序](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)文档。 

> [!IMPORTANT]
> 若要接受和接收 Azure Data Share，你必须先注册 Microsoft.DataShare 资源提供程序，而且你必须是在其中接受数据的存储帐户的所有者。 按[排查 Azure Data Share 预览版问题](data-share-troubleshoot.md)中记录的说明注册数据共享资源提供程序，并将你自己添加为存储帐户的所有者。 

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="open-invitation"></a>打开邀请

检查收件箱中是否有来自数据提供者的邀请。 邀请来自 Microsoft Azure，标题为“来自 <yourdataprovider@domain.com> 的 Azure Data Share 邀请”。  记下共享名称，确保接受的是正确的共享（如果有多个邀请）。 

选择“查看邀请”，在 Azure 中查看邀请。  这样会转到“已接收共享”视图。

![邀请](./media/invitations.png "邀请列表") 

选择要查看的共享。 

## <a name="accept-invitation"></a>接受邀请
确保查看所有字段，包括“使用条款”。  如果同意使用条款，则必须勾选表示同意的框。 

![使用条款](./media/terms-of-use.png "使用条款") 

在“目标 Data Share 帐户”下选择要在其中部署 Data Share 的订阅和资源组。  

对于“Data Share 帐户”字段，如果没有现有的 Data Share 帐户，请选择“新建”。   否则，请选择一个现有的 Data Share 帐户来接受数据共享。 

对于“已接收共享的名称”字段，可以保留数据提供者指定的默认值，也可以为已接收共享指定新名称。  

![目标数据共享帐户](./media/target-data-share.png "目标数据共享帐户") 

同意使用条款并指定共享位置以后，请选择“接受并配置”。  如果你选择此选项，系统会创建一个共享订阅，下一个屏幕会要求你选择一个目标存储帐户，以便将数据复制到其中。 

![接受选项](./media/accept-options.png "接受选项") 

如果首选现在接受邀请但在稍后配置存储，则请选择“接受并稍后配置”。  此选项允许在以后配置目标存储帐户。 若要在以后继续配置存储，请参阅[如何配置存储帐户](how-to-configure-mapping.md)页，其中列出的详细步骤演示了如何继续进行数据共享配置。 

如果不想接受邀请，请选择“拒绝”。  

## <a name="configure-storage"></a>配置存储
在“目标存储设置”下，  选择“订阅”、“资源组”以及用于接收数据的存储帐户。 

![目标存储设置](./media/target-storage-settings.png "目标存储") 

若要接收定期刷新的数据，请确保启用快照设置。 请注意，如果数据提供者已在数据共享中包括了快照设置计划，你只会看到一个这样的计划。 

![快照设置](./media/snapshot-settings.png "快照设置") 

选择“保存”。  

## <a name="trigger-a-snapshot"></a>触发快照

可以在“已接收共享”->“详细信息”选项卡中触发快照，只需选择“触发快照”即可。  在这里，可以触发数据的完整或增量快照。 如果是首次从数据提供者处接收数据，请选择完整副本。 

![触发快照](./media/trigger-snapshot.png "触发快照") 

如果上次运行状态为“成功”，请打开存储帐户查看接收的数据。  

若要检查使用了哪个存储帐户，请选择“数据集”。  

![使用者数据集](./media/consumer-datasets.png "使用者数据集映射") 

## <a name="view-history"></a>查看历史记录
若要查看快照历史记录，请导航到“已接收共享”->“历史记录”。 在这里可以找到一个历史记录，其中包含过去 60 天生成的的所有快照。 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何接受和接收 Azure Data Share。 若要详细了解 Azure Data Share 概念，请继续阅读[概念：Azure Data Share 术语](terminology.md)。