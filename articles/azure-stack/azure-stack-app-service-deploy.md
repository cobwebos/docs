---
title: 部署应用服务：Azure Stack | Microsoft Docs
description: 在 Azure Stack 中部署应用服务的详细指南
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: 22593fc470325fbfb74cfb432207abeea7d96ac2
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342778"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>将应用服务资源提供程序添加到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用本文中的指南来部署 Azure Stack 中的应用服务。

>[!IMPORTANT]  
>将 1804年更新应用于 Azure Stack 集成系统，或在部署 Azure 应用服务 1.2 之前部署最新 Azure Stack 开发工具包 (ASDK)。

您可以使用户能够创建 web 应用和 API 应用程序。 若要允许用户创建这些应用程序，需要：

 - 添加[应用服务资源提供程序](azure-stack-app-service-overview.md)到 Azure Stack 部署使用本文中所述的步骤。
 - 安装应用服务资源提供程序后，可以将其包括在你的产品/服务和计划。 然后，用户可以订阅以获取服务并开始创建应用程序。

> [!IMPORTANT]  
> 运行资源提供程序安装程序之前，请确保已按照中的指导[在开始之前](azure-stack-app-service-before-you-get-started.md)。

## <a name="run-the-app-service-resource-provider-installer"></a>运行应用服务资源提供程序安装程序

应用服务资源提供程序安装在至少需要一小时。 所需的时间长度取决于多少角色实例您部署。 部署过程中，安装程序将运行以下任务：

 - 在指定的 Azure Stack 存储帐户中创建 blob 容器。
 - 为应用服务中创建 DNS 区域和条目。
 - 注册应用服务资源提供程序。
 - 注册应用服务库项。

若要部署应用服务资源提供程序，请执行以下步骤：

1. 从可以访问 Azure Stack 管理员 Azure 资源管理终结点的计算机，以管理员身份运行 appservice.exe。

2. 选择**部署应用服务或升级到最新版本**。

    ![应用服务安装程序][1]

3. 查看并接受 Microsoft 软件许可条款，然后选择**下一步**。

4. 查看并接受第三方许可条款，然后选择**下一步**。

5. 请确保应用服务云配置信息正确无误。 如果在 Azure Stack 开发工具包 (ASDK) 部署过程中使用的默认设置，你可以接受默认值。 但是，如果在部署 ASDK 中，或要部署 Azure Stack 集成系统时，在自定义选项，则必须编辑此窗口以反映差异中的值。

   例如，如果使用域后缀 mycloud.com，则必须将“Azure Stack 租户”Azure 资源管理器终结点更改为 management.&lt;区域&gt;.mycloud.com。 查看这些设置，并选择**下一步**以保存设置。

   ![应用服务安装程序][2]

6. 在下一步应用服务安装程序页上，请执行以下步骤：

    a. 选择**Connect**旁边**Azure Stack 订阅**。

     - 如果使用 Azure Active Directory (Azure AD)，请输入在部署 Azure Stack 时提供的 Azure AD 管理员帐户和密码。 选择**登录**。
     - 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，cloudadmin@azurestack.local。 输入你的密码，并选择**Sign In**。

   b. 在中**Azure Stack 订阅**，选择**默认提供商订阅**。

     >[!NOTE]
     >目前，应用服务可以只部署到**默认提供商订阅**。

   c. 在中**Azure Stack 位置**，选择对应于要部署到的区域的位置。 例如，如果要部署到 Azure Stack 开发工具包，请选择“本地”。

    ![应用服务安装程序][3]

7. 现在可以将它们部署到现有的虚拟网络配置[使用这些步骤](azure-stack-app-service-before-you-get-started.md#virtual-network)，或让应用服务安装程序创建新的虚拟网络和子网。 若要创建 VNet，请执行以下步骤：

   a. 选择**使用默认设置创建 VNet**，接受默认值，并选择**下一步**。

   b. 或者，选择**使用现有的 VNet 和子网**。 完成以下操作：

     - 选择**资源组**，其中包含你的虚拟网络。
     - 选择**虚拟网络**你想要将部署到的名称。
     - 选择正确**子网**值为每个所需的角色子网。
     - 选择“**下一步**”。

   ![应用服务安装程序][4]

8. 输入你的文件共享的信息，然后选择**下一步**。 文件共享的地址必须使用完全限定域 (FQDN) 或你的文件服务器的 IP 地址。 例如 \\\appservicefileserver.local.cloudapp.azurestack.external\websites，或 \\\10.0.0.1\websites。

   >[!NOTE]
   >安装程序会尝试测试与继续操作之前的文件共享的连接。 但是，如果你正在部署到现有的虚拟网络，此连接测试可能文件。 将向你提供一条警告和提示以继续。 如果文件共享信息是否正确，继续部署。

   ![应用服务安装程序][7]

9. 在下一步应用服务安装程序页上，请执行以下步骤：

   a. 在“标识应用程序 ID”框中，输入要用于标识的应用程序 GUID（来自 Azure AD）。

   b. 在“标识应用程序证书文件”框中，输入（或浏览到）证书文件的位置。

   c. 在“标识应用程序证书密码”框中，输入证书的密码。 此密码是在使用脚本创建证书时记下的密码。

   d. 在“Azure 资源管理器根证书文件”框中，输入（或浏览到）证书文件的位置。

   e. 选择“**下一步**”。

   ![应用服务安装程序][9]

10. 对于每个的三个证书文件框中，选择**浏览**并导航到相应的证书文件。 必须为每个证书提供密码。 这些证书是在[创建所需证书步骤](azure-stack-app-service-before-you-get-started.md#get-certificates)中创建的。 选择**下一步**后输入所有信息。

    | Box | 证书文件名示例 |
    | --- | --- |
    | **应用服务默认 SSL 证书文件** | \_.appservice.local.AzureStack.external.pfx |
    | **应用服务 API SSL 证书文件** | api.appservice.local.AzureStack.external.pfx |
    | **应用服务发布者 SSL 证书文件** | ftp.appservice.local.AzureStack.external.pfx |

    如果在创建证书时使用了其他域后缀，证书文件名不要使用 *local.AzureStack.external*， 而要改用自定义域信息。

    ![应用服务安装程序][10]

11. 输入用来托管应用服务资源提供程序数据库，然后选择的服务器实例的 SQL Server 详细信息**下一步**。 安装程序将验证 SQL 连接属性。

    > [!NOTE]
    > 安装程序会尝试测试继续之前的 SQL server 的连接。 但是，如果你正在部署到现有的虚拟网络，此连接测试可能文件。 将向你提供一条警告和提示以继续。 如果 SQL Server 信息是否正确，继续部署。

    ![应用服务安装程序][11]

12. 查看角色实例和 SKU 选项。 默认设置中填充了 ASDK 部署中每个角色的最小实例数和最低 SKU 层级。 提供 vCPU 和内存要求摘要是为了帮助你规划部署。 进行选择后，选择**下一步**。

    >[!NOTE]
    >对于生产部署，请按照 [Azure Stack 中 Azure 应用服务服务器角色的容量规划](azure-stack-app-service-capacity-planning.md)中的指南进行操作。

    | 角色 | 最小实例数 | 最小 SKU | 说明 |
    | --- | --- | --- | --- |
    | 控制器 | 1 | Standard_A2 -（2 个 vCPU，3584 MB） | 管理和维护应用服务云的运行状况。 |
    | 管理 | 1 | Standard_A2 -（2 vCPU，3584 MB） | 管理应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员门户、租户门户、Functions 门户）和数据服务。 为了支持故障转移，已将建议的实例数增加到 2 个。 |
    | 发布者 | 1 | Standard_A1 -（1 vCPU，1792 MB） | 通过 FTP 和 Web 部署发布内容。 |
    | FrontEnd | 1 | Standard_A1 -（1 vCPU，1792 MB） | 将请求路由到应用服务应用程序。 |
    | 共享辅助角色 | 1 | Standard_A1 -（1 vCPU，1792 MB） | 托管 Web 应用程序或 API 应用程序和 Azure Functions 应用。 可能需要添加更多实例。 作为操作员，可以定义产品/服务，并选择任何 SKU 层。 这些层必须至少具有一个 vCPU。 |

    ![应用服务安装程序][13]

    >[!NOTE]
    >**Windows Server 2016 Core 不是受支持的平台映像与 Azure Stack 上的 Azure 应用服务配合使用。不要将评估映像用于生产部署。**

13. 在“选择平台映像”框中选择 Windows Server 2016 虚拟机部署映像，该映像是应用服务云的计算资源提供程序提供的映像之一。 选择“**下一步**”。

14. 在下一步应用服务安装程序页上，请执行以下步骤：

     a. 输入辅助角色虚拟机管理员用户名和密码。

     b. 输入其他角色虚拟机管理员用户名和密码。

     c. 选择“**下一步**”。

    ![应用服务安装程序][15]

15. 在应用服务安装程序的摘要页上，请执行以下步骤：

    a. 验证所做的选择。 若要进行更改，请使用“上一步”按钮访问前面的页面。

    b. 如果配置正确，则选中此复选框。

    c. 若要开始部署，请选择**下一步**。

    ![应用服务安装程序][16]

16. 在下一步应用服务安装程序页上，请执行以下步骤：

    a. 跟踪安装进度。 Azure Stack 上的应用服务大约需要 60 分钟才能完成基于默认选择的部署。

    b. 安装程序已成功完成后，选择**退出**。

    ![应用服务安装程序][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>验证 Azure Stack 上的应用服务安装

1. 在 Azure Stack 管理员门户中，转到“管理 - 应用服务”。

2. 在状态下的概览中，检查“状态”是否显示“所有角色已就绪”。

    ![应用服务管理](media/azure-stack-app-service-deploy/image12.png)

   >[!IMPORTANT]
   >如果你正在部署到现有的虚拟网络和使用内部 IP 地址连接到你的文件服务器，必须添加出站安全规则。 此规则启用辅助子网和文件服务器之间的 SMB 流量。  为此，请转到管理门户中的 WorkersNsg 并添加具有以下属性的出站安全规则：<br>
    >  - 源：任何
    >  - 源端口范围：*
    >  - 目标：IP 地址
    >  - 目标 IP 地址范围：文件服务器的 IP 范围
    >  - 目标端口范围：445
    >  - 协议：TCP
    >  - 操作：允许
    >  - 优先级：700
    >  - 名称：Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>体验 Azure Stack 上的应用服务

部署并注册应用服务资源提供程序后，对其进行测试以确保用户可以部署 Web 应用和 API 应用。

>[!NOTE]
>您需要创建产品/服务计划中有 Microsoft.Web 命名空间。 您还需要订阅产品/服务的租户订阅。 有关详细信息，请参阅[创建套餐](azure-stack-create-offer.md)和[创建计划](azure-stack-create-plan.md)。
>
>必须有租户订阅，才能创建使用 Azure Stack 上的应用服务的应用程序。 与应用服务资源提供程序管理相关的服务管理员可以在管理门户中完成的唯一任务。 这包括添加容量、 配置部署源，以及添加辅助角色层和 Sku。
>
>若要创建 Web 应用、API 应用和 Azure Functions 应用，必须使用租户门户并具有租户订阅。
>

若要创建一个测试 web 应用，请执行以下步骤：

1. 在 Azure Stack 租户门户中，选择**新建** > **Web + 移动** > **Web 应用**。

2. 下**Web 应用**，输入中的名称**Web 应用**。

3. 下**资源组**，选择**新建**。 输入的名称**资源组**。

4. 选择**应用服务计划/位置** > **新建**。

5. 下**应用服务计划**，输入的名称**应用服务计划**。

6. 选择**定价层** > **免费共享**或**共享共享** > **选择** > **确定** > **创建**。

7. 新的 web 应用的磁贴将显示在仪表板上。 选择磁贴。

8. 上**Web 应用**，选择**浏览**若要查看此应用的默认网站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、DNN 或 Django 网站（可选）

1. 在 Azure Stack 租户门户中，选择 **+** 转到 Azure Marketplace，部署 Django 网站，，然后等待部署完成。 Django Web 平台使用基于文件系统的数据库。 它不需要任何其他资源提供程序，如 SQL 或 MySQL。

2. 如果还部署了 MySQL 资源提供程序，则可从市场部署 WordPress 网站。 当系统提示输入数据库参数时，请输入用户名，其格式为 *User1@Server1*（使用所选的用户名和服务器名称）。

3. 如果还部署了 SQL Server 资源提供程序，则可从市场部署 DNN 网站。 当系统提示输入数据库参数时，请在运行 SQL Server 的计算机中选择连接到资源提供程序的数据库。

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)。

 - [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
 - [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png