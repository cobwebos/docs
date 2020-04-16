---
title: 在 Azure 同步分析中预配和保护链接服务
description: 了解如何使用托管 Vnet 预配和保护链接服务
services: synapse-analytics
author: acomet
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 435c3fd6b1e6444fa3a31c68b4d74c2553d2e634
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430547"
---
# <a name="securing-a-linked-service-with-private-links"></a>使用专用链接保护链接服务 

在本文中，您将学习如何使用专用终结点在 Synapse 中保护链接服务。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请先创建一个[免费的 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：使用 Azure 数据湖第 2 代作为*源*数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md)以执行创建存储帐户的步骤。 确保存储帐户具有用于访问它的 Synapse Studio IP 筛选，并且您只允许**选定网络**访问存储帐户。 刀片**防火墙和虚拟网络**下的设置应如下所示。

![安全存储帐户](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>使用专用链接创建链接服务

在 Azure Synapse 分析中，链接服务是定义与其他服务的连接信息的位置。 在本节中，您将将 Azure 突触分析和 Azure 数据湖第 2 代作为链接服务添加。

1. 打开 Azure 同步工作室并转到 **"管理**"选项卡。
1. 在 **"外部连接**"下，选择 **"链接的服务**"。
1. 要添加链接的服务，请单击"**新建**"。
1. 从列表中选择 Azure 数据存储湖存储 Gen2 磁贴，然后单击"**继续**"。
1. 请确保启用**交互式创作**。 启用可能需要大约 1 分钟。 
1. 输入身份验证凭据。 帐户密钥、服务主体和托管标识当前受支持身份验证类型。 单击测试连接以验证您的凭据是否正确。
1. 选择**测试连接**，它应该失败，因为存储帐户不允许访问它，没有创建和批准专用终结点。 在错误消息中，应看到一个链接来创建**私有终结点**，您可以遵循该链接转到下一部分。 如果遵循该链接，请跳过下一部分。
1. 完成后，选择“创建”****。

## <a name="create-a-managed-private-endpoint"></a>创建托管专用终结点

如果测试上述连接时未单击超链接，请遵循以下路径。 现在，您需要创建一个托管专用终结点，您将连接到上面创建的链接服务。

1. 转到 **"管理"** 选项卡。
1. 转到**托管虚拟网络**部分。
1. 选择 **= 托管**专用终结点下的新增功能。
1. 从列表中选择 Azure 数据湖存储 Gen2 磁贴，然后选择"**继续**"。
1. 输入您在上面创建的存储帐户的名称。
1. 选择 **"创建"**
1. 您应该在等了几秒钟后看到创建的专用链接需要批准。

## <a name="approval-of-a-private-link"></a>批准专用链接
1. 选择上面创建的专用终结点。 您可以在存储帐户级别看到允许您批准专用终结点的超链接。 *另一种方法是直接转到 Azure 门户存储帐户并转到**专用终结点连接**边栏选项卡。*
1. 勾选您在 Studio 中创建的专用终结点，然后选择 **"批准**"。
1. 添加说明并单击 **"是"**
1. 返回"**管理**选项卡"的 **"托管虚拟网络**"部分下的 Synapse 工作室。
1. 大约需要 1 分钟才能为私有终结点反映批准。

## <a name="check-the-connection-works"></a>检查连接工作
1. 转到 **"管理**"选项卡并选择您创建的链接服务。
1. 确保**交互式创作**处于活动状态。
1. 选择“测试连接”****。 您应该看到连接成功。

现在，您已经建立了 Synapse 和链接服务之间的安全和私人连接！

## <a name="next-steps"></a>后续步骤

要进一步了解 Synapse 分析中的托管专用终结点，请参阅[Synapse 托管专用终结点的概念](data-integration-data-lake.md)一文。

有关 Synapse 分析的数据集成的详细信息，请参阅将数据[引入数据湖](data-integration-data-lake.md)一文。