---
title: 保护链接服务
description: 了解如何使用托管 VNet 预配和保护链接的服务
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f5a3c73d60f038820de100f99c554eec27fd6f55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033177"
---
# <a name="secure-a-linked-service-with-private-links"></a>使用专用链接保护链接服务 

本文介绍如何使用专用终结点保护 Synapse 中的链接服务。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**，使用 Azure Data Lake Gen 2 作为源数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md)来了解创建步骤。 请确保存储帐户具有 Synapse Studio IP 筛选用于访问它，并且你只允许 **所选网络** 访问存储帐户。 边栏选项卡 **和虚拟网络** 下的设置应该如下图所示。

![安全存储帐户](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>使用专用链接创建链接服务

在 Azure Synapse Analytics 中，链接服务是定义到其他服务的连接信息的一个位置。 在本部分中，你将添加 Azure Synapse Analytics 和 Azure Data Lake Gen 2 作为链接服务。

1. 打开 Azure Synapse Studio 并中转到 " **管理** " 选项卡。
1. 在“外部连接”下，选择“链接服务”。
1. 若要添加链接服务，请选择 " **新建**"。
1. 从列表中选择 "Azure Data Lake Storage Gen2" 磁贴，然后选择 " **继续**"。
1. 请确保启用“交互式创作”。 启用可能需要大约 1 分钟的时间。 
1. 输入你的身份验证凭据。 帐户密钥、服务主体和托管标识是目前支持的身份验证类型。 选择 "测试连接" 以验证你的凭据是否正确。
1. 选择 " **测试连接**"，它应该会失败，因为存储帐户不允许对其进行访问，而无需创建和批准专用终结点。 在错误消息中，你应会看到一个链接，用于创建 **专用终结点** ，你可以遵循此终结点来执行下一个部分。 如果单击该链接，请跳过下一部分。
1. 完成后，选择“创建”****。

## <a name="create-a-managed-private-endpoint"></a>创建托管专用终结点

如果在测试上述连接时未选择超链接，请遵循以下路径。 创建一个托管的专用终结点，该终结点将连接到上面创建的链接服务。

1. 转到“管理”选项卡。
1. 请参阅 **托管虚拟网络** 部分。
1. 选择 "托管专用终结点" 下的 " **+ 新建** "。
1. 从列表中选择 "Azure Data Lake Storage Gen2" 磁贴，然后选择 " **继续**"。
1. 输入前面创建的存储帐户的名称。
1. 选择“创建”
1. 等待几秒钟后，会显示创建的专用链接需要审批。

## <a name="private-link-approval"></a>私有链接审批
1. 选择前面创建的专用终结点。 你可以看到允许你在存储帐户级别批准专用终结点的超链接。 *一种替代方法是直接进入 Azure 门户的存储帐户，并进入 " **专用终结点连接** " 边栏选项卡。*
1. 勾选你在工作室中创建的专用终结点，并选择 " **批准**"。
1. 添加说明，然后选择 **"是"**
1. 返回到 "**管理**" 选项卡的 "**托管虚拟网络**" 部分下的 "Synapse Studio"。
1. 需要大约1分钟的时间才能获得为你的专用终结点反射的批准。

## <a name="check-the-connection-works"></a>检查连接工作
1. 中转到 " **管理** " 选项卡，然后选择已创建的链接服务。
1. 请确保 **交互式创作** 处于活动状态。
1. 选择“测试连接”。 应会看到连接成功。

现已在 Synapse 与链接服务之间建立安全的专用连接。

## <a name="next-steps"></a>后续步骤

若要进一步了解 Synapse Analytics 中托管的专用终结点，请参阅 [Synapse 托管专用终结点](data-integration-data-lake.md) 一文中的概念。

有关 Synapse Analytics 的数据集成的详细信息，请参阅将 [数据引入到 Data Lake 一](data-integration-data-lake.md) 文。
