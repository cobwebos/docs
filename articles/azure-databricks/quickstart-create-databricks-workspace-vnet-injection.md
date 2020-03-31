---
title: 在您自己的虚拟网络快速入门中创建 Azure 数据块工作区
description: 本文介绍如何将 Azure 数据块部署到虚拟网络。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132677"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>快速入门：在您自己的虚拟网络中创建 Azure 数据砖块工作区

Azure 数据砖的默认部署将创建由数据砖块管理的新虚拟网络。 此快速入门演示如何在您自己的虚拟网络中创建 Azure 数据块工作区。 您还可以在工作区中创建 Apache Spark 群集。 

有关为何选择在您自己的虚拟网络中创建 Azure 数据砖块工作区的详细信息，请参阅[在 Azure 虚拟网络中部署 Azure 数据块（VNet 注入）。](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

如果没有 Azure 订阅，请创建[一个免费帐户](https://azure.microsoft.com/free/databricks/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 Azure[门户](https://portal.azure.com/)。

> [!Note]
> 不能使用 Azure 免费试用订阅完成本教程****。
> 如果你有免费帐户，请转到个人资料并将订阅更改为“即用即付”****。 有关详细信息，请参阅 [Azure 免费帐户](https://azure.microsoft.com/free/)。 然后，[移除支出限制](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)，并为你所在区域的 vCPU [请求增加配额](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。 创建 Azure Databricks 工作区时，可以选择“试用版(高级 - 14天免费 DBU)”**** 定价层，让工作区访问免费的高级 Azure Databricks DBU 14 天。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在 Azure 门户菜单中，选择“创建资源”****。 然后选择**网络>虚拟网络**。

    ![在 Azure 门户上创建虚拟网络](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. 在 **"创建虚拟网络"** 下，应用以下设置： 

    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |订阅|用户的订阅\<\>|选择要使用的 Azure 订阅。|
    |资源组|数据砖-快速启动|选择 **"新建"** 并输入帐户的新资源组名称。|
    |“属性”|数据砖-快速启动|为虚拟网络选择名称。|
    |区域|\<选择离用户最近的区域\>|选择可以托管虚拟网络的地理位置。 使用最靠近用户的位置。|

    ![Azure 门户上虚拟网络的基础知识](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. 选择 **"下一步"：IP 地址>** 并应用以下设置。 然后选择 **"审阅 + 创建**"。
    
    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |IPv4 地址空间|10.2.0.0/16|以 CIDR 表示法表示法表示语的虚拟网络地址范围。 CIDR 范围必须介于 /16 和 /24 之间|
    |子网名称|default|为虚拟网络中的默认子网选择名称。|
    |子网地址范围|10.2.0.0/24|以 CIDR 表示法表示的子网地址范围， 它必须包含在虚拟网络的地址空间中。 无法编辑正在使用的子网的地址范围。|

    ![在 Azure 门户上为虚拟网络设置 IP 配置](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. 在 **"审阅 + 创建**"选项卡上，选择 **"创建**"以部署虚拟网络。 部署完成后，导航到虚拟网络，并在 **"设置"** 下选择 **"地址空间**"。 在"*添加其他地址范围*"框中，插入`10.179.0.0/16`并选择 **"保存**"。

    ![Azure 虚拟网络地址空间](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

1. 在 Azure 门户菜单中，选择“创建资源”****。 然后选择 **"分析>数据块**。

    ![在 Azure 门户上创建 Azure 数据砖块工作区](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. 在**Azure 数据砖块服务**下，应用以下设置：

    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |工作区名称|数据砖-快速启动|为 Azure 数据块工作区选择名称。|
    |订阅|用户的订阅\<\>|选择要使用的 Azure 订阅。|
    |资源组|数据砖-快速启动|选择用于虚拟网络的相同资源组。|
    |位置|\<选择离用户最近的区域\>|选择与虚拟网络相同的位置。|
    |定价层|选择“标准”或“高级”。|有关定价层的详细信息，请参阅[数据砖定价页](https://azure.microsoft.com/pricing/details/databricks/)。|

    ![创建 Azure 数据砖块工作区基础知识](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. 在 **"基础知识"** 页上输入完设置后，选择 **"下一步：网络>** 并应用以下设置：

    |设置|建议的值|描述|
    |-------|---------------|-----------|
    |在虚拟网络 （VNet） 中部署 Azure 数据砖块工作区|是|此设置允许您在虚拟网络中部署 Azure 数据砖块工作区。|
    |虚拟网络|数据砖-快速启动|选择您在上一节中创建的虚拟网络。|
    |公共子网名称|公共子网|使用默认的公共子网名称。|
    |公共子网 CIDR 范围|10.179.64.0/18|使用 CIDR 范围，最多包括 /26。|
    |专用子网名称|私子网|使用默认专用子网名称。|
    |专用子网 CIDR 范围|10.179.0.0/18|使用 CIDR 范围，最多包括 /26。|

    ![在 Azure 门户上将 VNet 信息添加到 Azure 数据砖块工作区](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. 部署完成后，导航到 Azure 数据砖块资源。 请注意，虚拟网络对等互连已禁用。 另请注意概述页中的资源组和托管资源组。 

    ![Azure 数据砖块概述在 Azure 门户中](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    托管资源组不可修改，并且不用于创建虚拟机。 您只能在管理的资源组中创建虚拟机。

    ![Azure 数据砖托管资源组](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    当工作区部署失败时，工作区仍将以失败状态创建。 删除失败的工作区并创建解决部署错误的新工作区。 删除失败的工作区时，托管资源组和任何成功部署的资源也会被删除。

## <a name="create-a-cluster"></a>创建群集

> [!NOTE]
> 若要使用免费帐户创建 Azure Databricks 群集，请在创建群集前转到你的配置文件并将订阅更改为**即用即付**。 有关详细信息，请参阅 [Azure 免费帐户](https://azure.microsoft.com/free/)。

1. 返回到 Azure 数据砖块服务，并在 **"概述"** 页上选择 **"启动工作区**"。

2. 选择**群集** > **= 创建群集**。 然后创建群集名称，如*数据砖块-快速启动群集*，并接受剩余的默认设置。 选择“创建群集”。****

    ![创建 Azure 数据砖块群集](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 群集运行后，返回到 Azure 门户中的托管资源组。 请注意新的虚拟机、磁盘、IP 地址和网络接口。 在每个具有 IP 地址的公共和专用子网中创建网络接口。  

    ![群集创建后 Azure 数据砖托管资源组](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. 返回到 Azure 数据砖块工作区并选择您创建的群集。 然后导航到**Spark UI**页上的 **"执行器**"选项卡。 请注意，驱动程序和执行器的地址在专用子网范围内。 在此示例中，驱动程序为 10.179.0.6，执行器为 10.179.0.4 和 10.179.0.5。 您的 IP 地址可能不同。

    ![Azure 数据砖火花 UI 执行器](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>清理资源

完成本文后，可以终止群集。 为此，请在 Azure Databricks 工作区的左窗格中选择“群集”****。 针对想要终止的群集，将光标移到“操作”**** 列下面的省略号上，选择“终止”**** 图标。 这将停止群集。

如果不手动终止群集，它将自动停止，前提是您在创建群集时选择了"创建群集时在**\_\_非活动几分钟后终止**"复选框。 在这种情况下，如果群集保持非活动状态超过指定的时间，则会自动停止。

如果不希望重用群集，可以删除在 Azure 门户中创建的资源组。

## <a name="next-steps"></a>后续步骤

在本文中，您将在 Azure 数据块中创建了一个 Spark 群集，该群集已部署到虚拟网络。 进入下一篇文章，了解如何使用 Azure 数据块笔记本中的 JDBC 在虚拟网络中查询 SQL Server Linux Docker 容器。  

> [!div class="nextstepaction"]
>[从 Azure 数据砖块笔记本查询虚拟网络中的 SQL Server Linux Docker 容器](vnet-injection-sql-server.md)
