---
title: Azure 门户：创建 SQL 数据库托管实例 | Microsoft Docs
description: 在 VNet 中创建 Azure SQL 数据库托管实例。
keywords: sql 数据库教程, 创建 sql 数据库托管实例
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 05/09/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: e337a5c7c203e2e1048149dfeff71436a4d2752f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850604"
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>在 Azure 门户中创建 Azure SQL 数据库托管实例

本教程演示如何使用 Azure 门户在虚拟网络 (VNet) 的专用子网中创建 Azure SQL 数据库托管实例（预览版），然后使用同一 VNet 中虚拟机上的 SQL Server Management Studio 连接到该托管实例。

> [!div class="checklist"]
> * 将订阅添加到允许列表
> * 配置虚拟网络 (VNet)
> * 创建新的路由表和路由
> * 将路由表应用到托管实例子网
> * 创建托管实例
> * 在适用于虚拟机的 VNet 中创建新的子网
> * 在 VNet 的新子网中创建虚拟机
> * 连接到虚拟机
> * 安装 SSMS 并连接到托管实例


如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

> [!IMPORTANT]
> 有关目前支持托管实例的区域列表，请参阅[使用 Azure SQL 数据库托管实例将数据库迁移到完全托管的服务](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/)。
 
## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/#create/Microsoft.SQLManagedInstance)。

## <a name="whitelist-your-subscription"></a>将订阅添加到允许列表

托管实例最初是作为封闭的公共预览版发布的，需要通过订阅将其加入允许列表。 如果订阅尚未加入允许列表，请执行下面提供的步骤，接受预览版条款，然后发送加入允许列表的请求。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到“托管实例”，然后选择“Azure SQL 数据库托管实例(预览版)”。
3. 单击“创建”。

   ![托管实例“创建”按钮](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. 选择订阅，单击“预览版条款”，然后提供请求的信息。

   ![托管实例预览版条款](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. 完成后，单击“确定”。

   ![托管实例预览版条款](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > 在等待预览版审核过程中，可以继续完成本教程的后续几个部分。

## <a name="configure-a-virtual-network-vnet"></a>配置虚拟网络 (VNet)

以下步骤说明如何创建新的可供托管实例使用的 [Azure 资源管理器](../azure-resource-manager/resource-manager-deployment-model.md)虚拟网络 (VNet)。 有关 VNet 配置的详细信息，请参阅[托管实例 VNet 配置](sql-database-managed-instance-vnet-configuration.md)。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到并单击“虚拟网络”，确认是否已选择“资源管理器”作为部署模型，然后单击“创建”。

   ![虚拟网络“创建”按钮](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 根据下表中的说明，在虚拟网络窗体中填充请求的信息：

   | 设置| 建议的值 | 说明 |
   | ------ | --------------- | ----------- |
   |**名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**地址空间**|任何有效的地址范围，例如 10.14.0.0/24|虚拟网络的地址名称，采用 CIDR 表示法。|
   |**订阅**|你的订阅|有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。|
   |**资源组**|任何有效的资源组（新的或现有的）|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**位置**|任何有效的位置| 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。|
   |**子网名称**|任何有效的子网名称，例如 mi_subnet|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**子网地址范围**|任何有效的子网地址，例如 10.14.0.0/28。 请使用比地址空间本身小的子网地址空间，以便留出空间在同一 VNet 中创建其他子网，例如用于托管测试应用/客户端应用的子网，或者用于从本地或其他 VNet 进行连接的网关子网。|以 CIDR 表示法表示的子网地址范围， 必须包含在虚拟网络的地址空间中|
   |**服务终结点**|已禁用|请为此子网启用一个或多个服务终结点|
   ||||

   ![虚拟网络创建窗体](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. 单击“创建”。

## <a name="create-new-route-table-and-a-route"></a>创建新的路由表和路由

以下步骤演示如何创建 0.0.0.0/0 下一跃点 Internet 路由。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到并单击“路由表”，然后单击“路由表”页中的“创建”。 

   ![路由表“创建”按钮](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. 根据下表中的说明，在路由表窗体中填充请求的信息：

   | 设置| 建议的值 | 说明 |
   | ------ | --------------- | ----------- |
   |**名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**订阅**|你的订阅|有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。|
   |**资源组**|选择在前述过程中创建的资源组|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**位置**|选择在前面的过程中指定的位置| 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。|
   |**禁用 BCP 路由传播**|已启用||
   ||||

   ![路由表创建窗体](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. 单击“创建”。
5. 创建路由表后，打开新创建的路由表。

   ![路由表](./media/sql-database-managed-instance-tutorial/route-table.png)

6. 单击“路由”，然后单击“添加”。

   ![路由表“添加”按钮](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  根据下表中的说明，添加“0.0.0.0/0 下一跃点 Internet 路由”作为**唯一**路由：

    | 设置| 建议的值 | 说明 |
    | ------ | --------------- | ----------- |
    |**路由名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
    |**地址前缀**|0.0.0.0/0|此路由应用到的目标 IP 地址，以 CIDR 表示法表示。|
    |**下一跃点类型**|Internet|下一跃点处理此路由的匹配数据包|
    |||

    ![路由](./media/sql-database-managed-instance-tutorial/route.png)

8. 单击“确定”。

## <a name="apply-the-route-table-to-the-managed-instance-subnet"></a>将路由表应用到托管实例子网

以下步骤说明如何在托管实例子网上设置新的路由表。

1. 若要在托管实例子网上设置路由表，请打开此前创建的虚拟网络。
2. 单击“子网”，然后单击托管实例子网（以下屏幕截图中的 **mi_subnet**）。

    ![子网](./media/sql-database-managed-instance-tutorial/subnet.png)

11. 单击“路由表”，然后选择“myMI_route_table”。

    ![设置路由表](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. 单击“保存”

    ![设置路由表 - 保存](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>创建托管实例

以下步骤说明如何在预览版获得批准后创建托管实例。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到“托管实例”，然后选择“Azure SQL 数据库托管实例(预览版)”。
3. 单击“创建”。

   ![托管实例“创建”按钮](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. 选择订阅，并验证预览版条款是否显示“已接受”。

   ![托管实例预览版已接受](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. 根据下表中的说明，在托管实例窗体中填充请求的信息：

   | 设置| 建议的值 | 说明 |
   | ------ | --------------- | ----------- |
   |**托管实例名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**托管实例管理员登录名**|任何有效的用户名|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。 不要使用“serveradmin”，因为这是保留的服务器级角色。| 
   |**密码**|任何有效的密码|密码必须至少 16 个字符，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。|
   |**资源组**|之前创建的资源组||
   |**位置**|以前选择的位置|有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。|
   |**虚拟网络**|此前创建的虚拟网络|

   ![托管实例“创建”窗体](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. 单击“定价层”，设置计算和存储资源的大小并查看定价层选项。 默认情况下，实例获得 32 GB 的免费存储空间，这对应用程序来说可能不够。
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
> 子网中第一个实例的部署时间通常远超后续实例的部署时间 - 有时候需要 24 小时以上才能完成。 请勿因持续时间超出预期而取消部署操作。 第一个实例需要如此长的部署时间，但这只是暂时的。 在公共预览版发布后不久，部署时间预期会显著缩短。

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>在适用于虚拟机的 VNet 中创建新的子网

以下步骤说明了如何在适用于虚拟机的 VNet 中创建第二个子网。将在该虚拟机中安装 SQL Server Management Studio 并连接到托管实例。

1. 打开虚拟网络资源。
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. 单击“子网”，然后单击“+子网”。
 
   ![添加子网](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. 根据下表中的说明，在子网窗体中填充请求的信息：

   | 设置| 建议的值 | 说明 |
   | ------ | --------------- | ----------- |
   |**名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**地址范围(CIDR 块)**|VNet 中任何有效的地址范围（使用默认值）||
   |**网络安全组**|无||
   |**路由表**|无||
   |**服务终结点**|无||

   ![vm 子网详细信息](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. 单击“确定”。

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>在 VNet 的新子网中创建虚拟机

以下步骤说明如何在要创建托管实例的 VNet 中创建虚拟机。 

1. 在 Azure 门户的左上角单击“创建资源”。
2. 选择“计算”，然后选择“Windows Server 2016 Datacenter”或“Windows 10”。 教程的此部分使用 Windows Server。 配置 Windows 10 大体上类似。 

   ![计算](./media/sql-database-managed-instance-tutorial/compute.png)

3. 根据下表中的说明，在虚拟机窗体中填充请求的信息：

   | 设置| 建议的值 | 说明 |
   | ------ | --------------- | ----------- |
   |**名称**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   | **VM 磁盘类型**|SSD|SSD 的性价比最佳。|   
   |**用户名**|任何有效的用户名|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。| 
   |**密码**|任何有效的密码|密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。| 
   |**订阅**|你的订阅|有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。|
   |**资源组**|之前创建的资源组||
   |**位置**|以前选择的位置||
   |**已有 Windows 许可证**|否|如果拥有启用了软件保障 (SA) 的 Windows 许可证，可凭借 Azure 混合权益节省计算成本|
   ||||

   ![虚拟机“创建”窗体](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

4. 单击“确定”。
5. 为 VM 选择大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 本教程只需小型虚拟机。

    ![VM 大小](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

6. 单击“选择”。
7. 在“设置”窗体中单击“子网”，然后选择“vm_subnet”。 请勿选择在其中预配了托管实例的子网，而应选择同一 Vnet 中的另一子网。

    ![VM 设置](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

8. 单击“确定”。
9. 在摘要页上查看产品/服务详细信息，然后单击“创建”，开始虚拟机部署。
 
## <a name="connect-to-virtual-machine"></a>连接到虚拟机

以下步骤说明如何通过远程桌面连接连接到新创建的虚拟机。

1. 部署完成后，转到虚拟机资源。

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. 单击虚拟机属性上的“连接”按钮。 此时会创建和下载远程桌面协议文件（.rdp 文件）。
3. 若要连接到 VM，请打开下载的 RDP 文件。 
4. 出现提示时，请单击“连接”。 在 Mac 上，需要一个 RDP 客户端，例如 Mac 应用商店提供的这个[远程桌面客户端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

5. 输入在创建虚拟机时指定的用户名和密码，单击“确定”。

6. 你可能会在登录过程中收到证书警告。 单击“是”或“继续”继续进行连接。

已连接到“服务器管理器”仪表板中的虚拟机。

> [!IMPORTANT]
> 请等到托管实例成功预配后再继续。 该实例预配以后，请在托管实例的“概览”选项卡的“托管实例”字段中检索实例的主机名。 该名称类似于：**drfadfadsfd.tr23.westus1-a.worker.database.windows.net**。

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>安装 SSMS 并连接到托管实例

以下步骤说明如何下载并安装 SSMS，然后连接到托管实例。

1. 在“服务器管理器”中，单击左窗格中的“本地服务器”。

    ![服务器管理器属性](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. 在“属性”窗格中单击“启用”，修改 IE 增强的安全性配置。
3. 在“Internet Explorer 增强的安全性配置”对话框的“管理员”部分单击“关闭”，然后单击“确定”。

    ![internet explorer 增强的安全性配置](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. 从任务栏打开 **Internet Explorer**。
5. 选择“使用推荐的安全性和兼容性设置”，然后单击“确定”，完成 Internet Explorer 11 的设置。
6. 在 URL 地址框中输入 https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms，并单击 **Enter**。 
7. 下载最新版本的 SQL Server Management Studio，在系统提示时单击“运行”。
8. 在系统提示时，单击“安装”即可开始。
9. 安装完成后，单击“关闭”。
10. 打开 SSMS。
11. 在“连接到服务器”对话框的“服务器名称”框中输入托管实例的主机名*，选择“SQL Server 身份验证”，提供登录名和密码，然后单击“连接”。

    ![ssms 连接](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

连接后，可以在“数据库”节点中查看系统和用户数据库，以及在“安全性”、“服务器对象”、“复制”、“管理”、“SQL Server 代理”和“XEvent 探查器”节点中查看各种对象。

> [!NOTE]
> 若要将现有 SQL 数据库还原到托管实例，可以使用[用于迁移的 Azure 数据库迁移服务 (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) 从数据库备份文件还原，使用 [T-SQL RESTORE 命令](sql-database-managed-instance-restore-from-backup-tutorial.md)从数据库备份文件还原，或[从 BACPAC 文件导入](sql-database-import.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何使用 Azure 门户在虚拟网络 (VNet) 的专用子网中创建 Azure SQL 数据库托管实例（预览版），然后使用同一 VNet 中虚拟机上的 SQL Server Management Studio 连接到该托管实例。  你已了解如何： 

> [!div class="checklist"]
> * 将订阅添加到允许列表
> * 配置虚拟网络 (VNet)
> * 创建新的路由表和路由
> * 将路由表应用到托管实例子网
> * 创建托管实例
> * 在适用于虚拟机的 VNet 中创建新的子网
> * 在 VNet 的新子网中创建虚拟机
> * 连接到虚拟机
> * 安装 SSMS 并连接到托管实例

转到下一教程，了解如何将数据库备份还原到 Azure SQL 数据库托管实例。

> [!div class="nextstepaction"]
>[将数据库备份还原到 Azure SQL 数据库托管实例](sql-database-managed-instance-restore-from-backup-tutorial.md)
