---
title: 在虚拟网络中创建 Azure Databricks 工作区
description: 本文介绍如何将 Azure Databricks 部署到你的虚拟网络。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770481"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>快速入门：在虚拟网络中创建 Azure Databricks 工作区

本快速入门介绍如何在虚拟网络中创建 Azure Databricks 工作区。 此外将创建该工作区中的 Apache Spark 群集。

如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在 Azure 门户中，选择**创建资源** > **网络** > **虚拟网络**。

2. 下**创建虚拟网络**，应用以下设置： 

    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |名称|databricks-quickstart|选择虚拟网络的名称。|
    |地址空间|10.1.0.0/16|用 CIDR 标记来表示的虚拟网络地址范围。|
    |订阅|用户的订阅\<\>|选择要使用的 Azure 订阅。|
    |资源组|databricks-quickstart|选择**创建新**并输入你的帐户的新资源组名称。|
    |Location|\<选择离用户最近的区域\>|选择可以在其中托管你的虚拟网络的地理位置。 使用最靠近用户的位置。|
    |子网名称|default|选择虚拟网络中的默认子网的名称。|
    |子网地址范围|10.1.0.0/24|以 CIDR 表示法表示的子网地址范围， 它必须包含虚拟网络的地址空间。 无法编辑正在使用中的子网的地址范围。|

    ![在 Azure 门户中创建虚拟网络](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. 部署完成后，导航到你的虚拟网络并选择**地址空间**下**设置**。 在框中*添加额外的地址范围*，插入`10.179.0.0/16`，然后选择**保存**。

    ![Azure 虚拟网络地址空间](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

1. 在 Azure 门户中，选择**创建资源** > **Analytics** > **Databricks**。

2. 下**Azure Databricks 服务**，应用以下设置：

    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |工作区名称|databricks-quickstart|选择 Azure Databricks 工作区的名称。|
    |订阅|用户的订阅\<\>|选择要使用的 Azure 订阅。|
    |资源组|databricks-quickstart|选择用于虚拟网络的同一资源组。|
    |Location|\<选择离用户最近的区域\>|选择与你的虚拟网络位于同一位置。|
    |定价层|选择标准或高级。|有关定价层的详细信息，请参阅[Databricks 价格页](https://azure.microsoft.com/pricing/details/databricks/)。|
    |部署虚拟网络中的 Azure Databricks 工作区|是|此设置，可以部署在虚拟网络中的 Azure Databricks 工作区。|
    |虚拟网络|databricks-quickstart|选择在上一节中创建的虚拟网络。|
    |公共子网名称|public-subnet|使用默认公共子网名称。|
    |公共子网 CIDR 范围|10.179.64.0/18|为此子网的 CIDR 范围应介于/18 和/26。|
    |专用子网名称|private-subnet|使用默认的专用子网名称。|
    |专用子网 CIDR 范围|10.179.0.0/18|为此子网的 CIDR 范围应介于/18 和/26。|

    ![在 Azure 门户中创建 Azure Databricks 工作区](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. 部署完成后，导航到 Azure Databricks 资源。 请注意，禁用虚拟网络对等互连。 另请注意资源组和托管的资源组中概述页。 

    ![在 Azure 门户中的 azure Databricks 概述](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    托管的资源组包含的存储帐户 (DBFS)，辅助角色-sg （网络安全组） 工作线程-vnet （虚拟网络） 的物理位置。 它也是将在其中创建虚拟机、 磁盘、 IP 地址和网络接口的位置。 此资源组锁定默认设置。但是当群集虚拟网络中，托管的资源组中的工作人员 vnet 和"中心"虚拟网络之间创建网络接口。

    ![Azure Databricks 托管的资源组](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>创建群集

> [!NOTE]
> 若要使用免费帐户创建 Azure Databricks 群集，请在创建群集前转到你的配置文件并将订阅更改为**即用即付**。 有关详细信息，请参阅 [Azure 免费帐户](https://azure.microsoft.com/free/)。

1. 返回到 Azure Databricks 服务，然后选择**启动工作区**上**概述**页。

2. 选择**群集** > **+ 创建群集**。 然后创建一个群集名称，例如*databricks 快速入门群集*，并接受其余默认设置。 选择“创建群集”。

    ![创建 Azure Databricks 群集](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 群集运行后，返回到 Azure 门户中的托管的资源组。 请注意，新的虚拟机、 磁盘、 IP 地址和网络接口。 在每个 IP 地址的公用和专用子网中创建网络接口。  

    ![Azure Databricks 托管的资源组创建群集后](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. 返回到 Azure Databricks 工作区并选择你创建的群集。 然后导航到**执行器**选项卡上**Spark UI**页。 请注意，驱动程序和执行器的地址是专用子网范围内。 在此示例中，该驱动程序是 10.179.0.6 和执行器是 10.179.0.4 和 10.179.0.5。 你的 IP 地址可能不同。

    ![Azure Databricks Spark UI 执行器](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>清理资源

完成本文后，可以终止群集。 为此，请在 Azure Databricks 工作区的左窗格中选择“群集”。 针对想要终止的群集，将光标移到“操作”列下面的省略号上，选择“终止”图标。 这将停止该群集。

如果不手动终止群集，但在创建群集时选中了“在不活动 \_\_ 分钟后终止”复选框，则该群集会自动停止。 在这种情况下，如果群集保持非活动状态超过指定的时间，则会自动停止。

如果不希望重复使用该群集，则可以删除在 Azure 门户中创建的资源组。

## <a name="next-steps"></a>后续步骤

在本文中，您将创建在部署到虚拟网络的 Azure Databricks 中的 Spark 群集。 转到下一步的文章，了解如何查询从 Azure Databricks notebook 中使用 JDBC 的虚拟网络中的 SQL Server Linux Docker 容器。  

> [!div class="nextstepaction"]
>[查询 Azure Databricks 笔记本中的虚拟网络中的 SQL Server Linux Docker 容器](vnet-injection-sql-server.md)
