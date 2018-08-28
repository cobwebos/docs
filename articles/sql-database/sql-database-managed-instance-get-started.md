---
title: Azure 门户：创建 SQL 托管实例 | Microsoft Docs
description: 创建用于访问的 SQL 托管实例、网络环境和客户端 VM。
keywords: sql 数据库教程, 创建 sql 托管实例
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "42023726"
---
# <a name="create-an-azure-sql-managed-instance"></a>创建 Azure SQL 托管实例

本快速入门逐步讲解如何在 Azure 中创建 SQL 托管实例。 Azure SQL 数据库托管实例是一种“平台即服务 (PaaS)”SQL Server 数据库引擎实例，可用于在 Azure 云中运行和缩放高可用性 SQL Server 数据库。 本快速入门介绍如何通过创建 SQL 托管实例来入门。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="prepare-network-environment"></a>准备网络环境

SQL 托管实例是一种安全的服务，放置在你自己的 Azure 虚拟网络 (VNet) 中。 若要创建托管实例，需准备 Azure 网络环境，其中包括：
 - Azure VNet，将在其中放置托管实例。
 - Azure VNet 中的子网，将在其中放置托管实例。
 - 用户定义的路由，供托管实例与控制和管理实例的 Azure 服务通信。

子网专用于托管实例，不能在该子网中创建任何其他的资源（例如，Azure 虚拟机）。 在本快速入门中，将在 Azure VNet 中创建两个子网，以便将托管实例置于专用于托管实例的子网中，将其他资源置于默认子网中。

1. 单击以下按钮即可部署为 Azure SQL 数据库托管实例准备的 Azure 网络环境：

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    此按钮将在 Azure 门户中打开一个窗体，可以在其中配置网络环境，然后再部署它。

2. 可以更改 VNet 和子网的名称并调整与网络资源关联的 IP 范围。 然后按“购买”按钮，以便创建并配置环境：

    ![创建托管实例环境](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > 此 Azure 资源管理器部署将在 VNet 中创建两个子网 - 一个用于用于托管实例，称为 **ManagedInstances**，另一个称为 **Default**，用于其他资源，例如可以用来连接到托管实例的客户端虚拟机。 如果不需要两个子网，可以稍后删除默认子网；但在这种情况下，将无法完成本快速入门指南中的步骤 3 - [准备客户端计算机](#prepare-client-machine)。

    > [!Note]
    > 如果更改 VNet 和子网的名称，请确保记住新名称，因为以下部分需用到这些新名称。 在教程的其余部分，将会假定你已创建名为 **MyNewVNet** 的 VNet、用于 SQL 托管实例的 **ManagedInstances** 子网，以及用于虚拟机和其他资源的 **Default** 子网。

## <a name="create-a-managed-instance"></a>创建托管实例

以下步骤说明如何在预览版获得批准后创建托管实例。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到“托管实例”，然后选择“Azure SQL 数据库托管实例(预览版)”。
3. 单击“创建”。

   ![创建托管实例](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. 选择订阅，并验证预览版条款是否显示“已接受”。

   ![托管实例预览版已接受](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. 根据下表中的说明，在托管实例窗体中填充请求的信息：

   | 设置| 建议的值 | Description |
   | ------ | --------------- | ----------- |
   |**托管实例名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**托管实例管理员登录名**|任何有效的用户名|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。 不要使用“serveradmin”，因为这是保留的服务器级角色。| 
   |**密码**|任何有效的密码|密码必须至少 16 个字符，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。|
   |**资源组**|之前创建的资源组||
   |**位置**|以前选择的位置|有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。|
   |**虚拟网络**|此前创建的虚拟网络| 如果尚未更改上一步的名称，请选择 **MyNewVNet/ManagedInstances** 项。 否则，请选择在上一步输入的 VNet 名称和托管实例子网。 **请勿使用默认子网，因为该子网尚未进行配置，无法承载托管实例**。 |

   ![托管实例“创建”窗体](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. 单击“定价层”，设置计算和存储资源的大小并查看定价层选项。 默认情况下，实例获得 32 GB 的免费存储空间，**这对应用程序来说可能不够**。
7. 使用滑块或文本框指定存储量和虚拟核心数。 
   ![托管实例定价层](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. 完成后单击“应用”，保存选择的内容。  
9. 单击“创建”，部署托管实例。
10. 单击“通知”图标，查看部署的状态。
 
   ![部署进度](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. 单击“正在进行的部署”以打开“托管实例”窗口，进一步监视部署进度。
 
   ![部署进度 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

进行部署后，请继续下一过程。

> [!IMPORTANT]
> 子网中第一个实例的部署时间通常远超后续实例的部署时间 - 有时候需要 24 小时以上才能完成。 请勿因持续时间超出预期而取消部署操作。 第一个实例需要如此长的部署时间，但这只是暂时的。 在公共预览版发布后不久，部署时间预期会显著缩短。 在子网中创建第二个托管实例需数分钟。

## <a name="prepare-client-machine"></a>准备客户端计算机

由于 SQL 托管实例放置在专用虚拟网络中，因此需创建一个 Azure VM，其中需安装一些 SQL 客户端工具（例如 SQL Server Management Studio 或 SQL Operations Studio），以便连接到托管实例并执行查询。

> [!Note]
> 可以配置[点到站点](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)连接并从本地计算机连接到托管实例，不必使用客户端 Azure 虚拟机。

若要使用所有必需的工具创建一个客户端虚拟机，最容易的方式是使用 Azure 资源管理器模板。

1. 单击下面的按钮（确保在另一浏览器标签页中登录到 Azure 门户）：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. 在将要打开的窗体中，输入虚拟机的名称、管理员用户名和密码，这些是连接到 VM 所需的。

    ![创建客户端 VM](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    如果尚未更改 VNet 名称和默认子网，则不需更改后两个参数，否则应将这些值更改为设置网络环境时输入的值。

3. 单击“购买”按钮，然后 Azure VM 就会部署到已准备的网络中。

4. 使用远程桌面连接连接到 VM，找到已自动部署到 VM 上的 SQL Server Management Studio 或 SQL Operation Studio。

5. 打开 SSMS，在“服务器名称”框中输入托管实例的主机名，选择“SQL Server 身份验证”，在“连接到服务器”对话框中提供登录名和密码，然后单击“连接”。

    ![ssms 连接](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

连接后，可以在“数据库”节点中查看系统和用户数据库，以及在“安全性”、“服务器对象”、“复制”、“管理”、“SQL Server 代理”和“XEvent 探查器”节点中查看各种对象。

## <a name="next-steps"></a>后续步骤

 - [将应用程序连接到托管实例](sql-database-managed-instance-connect-app.md)。
 - [将数据库从本地迁移到托管实例](sql-database-managed-instance-migrate.md)。


