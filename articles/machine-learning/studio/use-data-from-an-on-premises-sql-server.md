---
title: 在 Azure 机器学习中使用本地 SQL Server | Microsoft 文档
description: 在 Azure 机器学习中使用本地 SQL Server 数据库中的数据执行高级分析。
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.openlocfilehash: c3402c824b70d357b68e71ed7cb5783a7489b2b0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837372"
---
# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>在 Azure 机器学习中使用本地 SQL Server 数据库中的数据执行高级分析

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

通常，使用本地数据的企业希望利用云的规模和灵活性来平衡其机器学习工作负荷。 但他们并不希望在将企业的本地数据移动到云时中断其当前业务处理和工作流。 Azure 机器学习现在支持从本地 SQL Server 数据库读取数据，并使用该数据训练和评分模型。 再也不必在云和本地服务器之间手动复制并同步数据。 相反，Azure 机器学习工作室中的**导入数据**模块现在可以为训练和评分作业直接从本地 SQL Server 数据库中读取。

本文概述了如何将本地 SQL Server 数据导入 Azure 机器学习。 假定用户熟悉工作区、模块、数据集、实验*等* Azure 机器学习概念。

> [!NOTE]
> 此功能不适用于免费工作区。 有关机器学习定价和层级的详细信息，请参阅 [Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-microsoft-data-management-gateway"></a>安装 Microsoft 数据管理网关
若要在 Azure 机器学习中访问本地 SQL Server 数据库，需要下载并安装 Microsoft 数据管理网关。 在机器学习工作室中配置网关连接时，将有机会使用如下所述的“下载并注册数据网关”对话框下载并安装网关。

还可以提前通过从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并运行 MSI 安装包来安装数据管理网关。
选择最新版本，选择 32 位版本还是选择 64 位版本取决于计算机。 MSI 还可以用于将现有数据管理网关升级到最新版本，同时保留所有设置。

该网关要求满足以下先决条件：

* 支持的 Windows 操作系统版本为 Windows 7、Windows 8/8.1、Windows 10、Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2。
* 推荐的网关计算机配置至少为 2 GHz，4 核，8 GB RAM 和 80 GB 磁盘。
* 如果主机计算机进入休眠状态，则网关不响应数据请求。 因此，安装网关之前，请在计算机上配置相应的电源计划。 如果计算机配置为休眠，网关安装会显示一条消息。
* 由于复制活动按特定频率发生，因此计算机上的资源使用率（CPU、内存）也遵循相同的高峰期和空闲期模式。 资源利用率很大程度上还取决于正在移动的数据量。 进行多个复制作业时，将观察到资源使用率在高峰期上升。 尽管以上所列最低配置从技术上讲足够，但你可能希望具有更多资源的配置（相对于最低配置），具体取决于数据移动的特定负载。

在设置并使用数据管理网关时，请考虑以下几点：

* 一台计算机上只能安装数据管理网关的一个实例。
* 可以将单个网关用于多个本地数据源。
* 可以将不同计算机上的多个网关连接到同一个本地数据源。
* 一次只为一个工作区配置网关。 目前，不能跨工作区共享网关。
* 可以为单个工作区配置多个网关。 例如，可能希望在开发期间使用与测试数据源连接的网关，而在准备实施时使用生产网关。
* 网关不需要位于数据源所在的计算机上。 但是，如果离数据源较近，可以减少网关连接到数据源的时间。 建议在不同于托管本地数据源的计算机上安装网关，以便网关和数据源不会因资源而竞争。
* 如果已在计算机中安装了为 Power BI 或 Azure 数据工厂方案提供服务的网关，那么在其他计算机上安装用于 Azure 机器学习的单独网关。

  > [!NOTE]
  > 数据管理网关和 Power BI 网关不能在同一台计算机上运行。
  >
  >
* 即使对其他数据使用 Azure ExpressRoute，也需要为 Azure 机器学习使用数据管理网关。 即使使用 ExpressRoute，也应将数据源视为本地数据源（位于防火墙之后）。 使用数据管理网关在机器学习和数据源之间建立连接。

有关安装先决条件、安装步骤和故障排除提示的详细信息，请参阅[数据管理网关](../../data-factory/v1/data-factory-data-management-gateway.md)一文。

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>将本地 SQL Server 数据库中的数据导入 Azure 机器学习
在本演练中，会在 Azure 机器学习工作区中安装数据管理网关、配置该网关，然后从本地 SQL Server 数据库中读取数据。

> [!TIP]
> 开始之前，请在浏览器中禁用 `studio.azureml.net` 的弹出窗口阻止程序。 如果使用的是 Google Chrome 浏览器，请下载并安装 Google Chrome WebStore [ClickOnce 应用扩展](https://chrome.google.com/webstore/search/clickonce?_category=extensions)中提供的几个插件中的一个。
>
>

### <a name="step-1-create-a-gateway"></a>步骤 1：创建网关
第一步是创建并设置用于访问本地 SQL 数据库的网关。

1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/Home/)，并选择要在其中工作的工作区。
2. 单击左侧的“设置”边栏选项卡，并单击顶部的“数据网关”选项卡。
3. 单击屏幕底部的“新建数据网关”按钮。

    ![新数据网关](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. 在“新建数据网关”对话框中，输入“网关名称”，还可添加“说明”。 单击右下角的箭头可转到配置的下一步。

    ![输入网关名称和说明](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. 在“下载并注册数据网关”对话框中，将网关注册密钥复制到剪贴板。

    ![下载并注册数据网关](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>如果尚未下载并安装 Microsoft 数据管理网关，则单击“下载数据管理网关”。 这会你将转到 Microsoft 下载中心，可以在其中选择所需网关版本、下载并安装它。 有关安装先决条件、安装步骤和故障排除提示的详细信息，请参阅[使用数据管理网关在本地资源和云之间移动数据](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)一文的开头部分。
7. 网关安装完成后，数据管理网关配置管理器将打开，还会显示“注册网关”对话框。 粘贴已复制到剪贴板的“网关注册密钥”，并单击“注册”。
8. 如果已安装网关，请运行数据管理网关配置管理器。 单击 **更改密钥** 、粘贴在上一步中已复制到剪贴板的 **网关注册密钥** ，并单击 **确定** 。
9. 安装完成后，会显示 Microsoft 数据管理网关配置管理器的“注册网关”对话框。 粘贴在上一步中已复制到剪贴板的“网关注册密钥”，并单击 **注册**。

    ![注册网关](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. 在 Microsoft 数据管理网关配置管理器的“主页”选项卡上设置了以下值后，网关配置即完成：

    * “网关名称”和“实例名称”设置为网关的名称。
    * “注册”设置为“已注册”。
    * “状态”设置为“已启动”。
    * 底部状态栏会显示“已连接到数据管理网关云服务”以及绿色核选标记。

      ![数据管理网关管理器](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure 机器学习工作室也会在注册完成时进行更新。

    ![网关注册成功](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. 在“下载并注册数据网关”对话框中，单击核选标记以完成设置。 “设置”页将网关状态显示为“联机”。 在右侧窗格中，可查看状态和其他有用信息。

    ![网关设置](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. 在 Microsoft 数据管理网关配置管理器中，切换到“证书”选项卡。此选项卡上指定的证书可用于加密/解密在门户上指定的本地数据存储的凭据。 此证书是默认证书。 Microsoft 建议将此证书更改为在证书管理系统中备份的自己的证书。 单击“更改”以改为使用自己的证书。

    ![更改网关证书](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. （可选）如果想要启用用于排查网关问题的详细日志记录，请在 Microsoft 数据管理网关配置管理中，切换到“诊断”选项卡，并选中“启用详细日志记录以进行疑难解答”选项。 可在“应用程序和服务日志” -&gt;“数据管理网关”节点下的“Windows 事件查看器”中找到该日志记录信息。 还可以使用“诊断”选项卡对使用网关连接本地数据源进行测试。

    ![启用详细日志记录](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

这完成了 Azure 机器学习中的网关设置过程。
现在，已可以使用本地数据。

可以在工作室中为每个工作区创建并设置多个网关。 例如，可能希望开发期间某个网关与测试数据源连接，而其他网关用于生产数据源。 Azure 机器学习使你可以灵活地设置多个网关，具体取决于企业环境。 目前，不能在工作区之间共享网关，一台计算机上只能安装一个网关。 有关详细信息，请参阅[使用数据管理网关在本地源与云之间移动数据](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)。

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>步骤 2：使用网关从本地数据源读取数据
在完成设置网关后，可以将“导入数据”模块添加到从本地 SQL Server 数据库输入数据的实验。

1. 在机器学习工作室中，选择“实验”选项卡、单击左下角的“+新建”，并选择“空白实验”（或选择提供的多个实验示例中的一个）。
2. 找到“导入数据”模块并将其拖动到试验画布上。
3. 单击画布下方的“另存为”。 输入“Azure 机器学习本地 SQL Server 教程”作为实验名称、选择工作区，并单击“确定”核选标记。

   ![使用新名称保存实验](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. 单击要选取的“导入数据”模块，并在画布右侧的“属性”窗格中，选择“数据源”下拉列表中的“本地 SQL 数据库”。
5. 选择已安装并注册的“数据网关”。 可以通过选择“(添加新的数据网关…)”来设置其他网关。

   ![为导入数据模块选择数据网关](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. 输入 SQL“数据库服务器名称”和“数据库名称”以及要执行的 SQL“数据库查询”。
7. 单击“用户名和密码”下的“输入值”，并输入你的数据库凭据。 可以使用 Windows 集成身份验证或 SQL Server 身份验证，具体取决于配置本地 SQL Server 的方式。

   ![输入数据库凭据](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   消息“必填值”将更改为带有绿色复选标记的“值已设置”。 只需输入凭据一次，除非数据库信息或密码发生更改。 Azure 机器学习使用在安装网关时提供的证书，来加密云中的凭据。 Azure 从不存储未加密的本地凭据。

   ![导入数据模块属性](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. 若要运行实验，请单击“运行”。

实验运行结束后，可以可视化从数据库中导入的数据，方法是单击“导入数据”模块的输出端口并选择“可视化”。

完成实验开发后，即可部署并操作模型。 通过使用 Batch 执行服务，可读取在“导入数据”模块中配置的本地 SQL Server 数据库中的数据，并将该数据用于评分。 尽管可以将请求响应服务用于评分本地数据，但 Microsoft 还是建议使用 [Excel 加载项](excel-add-in-for-web-services.md)。 目前，通过“导出数据”写入本地 SQL Server 数据库在实验和已发布的 Web 服务中都不受支持。
