---
title: "部署应用服务：Azure Stack | Microsoft Docs"
description: "在 Azure Stack 中部署应用服务的详细指南"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: anwestg
ms.openlocfilehash: b053d515949e71fcb5f1e520f6d3d5375cc27dcb
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>将应用服务资源提供程序添加到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

作为 Azure Stack 云操作员，可以让用户能够创建 Web 应用程序和 API 应用程序。 为此，必须先将[应用服务资源提供程序](azure-stack-app-service-overview.md)添加到 Azure Stack 部署，如本文中所述。 安装应用服务资源提供程序后，可以将其包含在产品/服务和计划中。 然后，用户可以订阅以获取服务并开始创建应用程序。

> [!IMPORTANT]
> 在运行安装程序之前，请确保已按照[开始之前](azure-stack-app-service-before-you-get-started.md)中的指南进行操作。
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>运行应用服务资源提供程序安装程序

App Service 资源提供程序安装到 Azure 堆栈环境可能需要至少一个小时的时间取决于你选择要部署多少个角色实例。 在此过程中，安装程序将执行以下操作：

* 在指定的 Azure Stack 存储帐户中创建 blob 容器。
* 为应用服务中创建 DNS 区域和条目。
* 注册应用服务资源提供程序。
* 注册应用服务库项。

若要部署应用服务资源提供程序，请执行以下步骤：

1. 以管理员身份运行 appservice.exe，从可以访问 Azure 堆栈管理 Azure 资源管理终结点的电脑。

2. 单击**部署 App Service 或升级到最新版本**。

    ![应用服务安装程序][1]

3. 查看并接受 Microsoft 软件许可条款，然后单击“下一步”。

4. 查看并接受第三方许可条款，然后单击“下一步”。

5. 请确保应用服务云配置信息正确无误。 如果在 Azure Stack 开发工具包部署过程中使用了默认设置，可以在此处接受默认值。 但是，如果你自定义选项，在部署 Azure 堆栈，或在集成的系统上部署时，你必须编辑此窗口以反映该中的值。 例如，如果你使用域后缀 mycloud.com，你的 Azure 堆栈租户 Azure 资源管理器终结点必须将更改为管理。&lt;区域&gt;。 mycloud.com。确认信息后，单击“下一步”。

    ![应用服务安装程序][2]

6. 在下一页上：
    1. 单击“Azure Stack 订阅”框旁边的“连接”按钮。
        * 如果使用 Azure Active Directory (Azure AD)，请输入在部署 Azure Stack 时提供的 Azure AD 管理员帐户和密码。 单击“登录”。
        * 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，cloudadmin@azurestack.local。 输入密码，并单击“登录”。
    2. 在**Azure 堆栈订阅**框中，选择**默认提供程序订阅**。
    3. 在“Azure Stack 位置”框中，选择要部署到的区域所对应的位置。 例如，如果要部署到 Azure Stack 开发工具包，请选择“本地”。

    ![应用服务安装程序][3]

4. 你现在可以选择配置完成的步骤将部署到现有的虚拟网络[此处](azure-stack-app-service-before-you-get-started.md#virtual-network)，或允许该应用程序服务的安装程序创建虚拟网络和关联的子网。
    1. 选择**使用默认设置创建 VNet**，接受默认设置，然后单击**下一步**，或;
    2. 选择**使用现有的 VNet 和子网**。
        1. 选择**资源组**，其中包含你的虚拟网络;
        2. 选择正确**虚拟网络**您想要部署到; 名称
        3. 选择正确**子网**为每个所需的角色的子网; 的值
        4. 单击“下一步”

    ![应用服务安装程序][4]

7. 输入文件共享的信息，然后单击“下一步”。 文件共享的地址必须使用完全限定的域名或你的文件服务器的 IP 地址。 例如， \\\appservicefileserver.local.cloudapp.azurestack.external\websites，或\\\10.0.0.1\websites。

   > [!NOTE]
   > 安装程序将尝试测试连接到文件共享，然后再继续。  但是如果你已选择要在现有虚拟网络中部署，安装程序可能无法连接到文件共享，并询问您是否要继续显示的警告。  请验证文件共享信息并继续它们是否正确。
   >
   >

   ![应用服务安装程序][7]

8. 在下一页上：
    1. 在“标识应用程序 ID”框中，输入要用于标识的应用程序 GUID（来自 Azure AD）。
    2. 在“标识应用程序证书文件”框中，输入（或浏览到）证书文件的位置。
    3. 在“标识应用程序证书密码”框中，输入证书的密码。 此密码是在使用脚本创建证书时记下的密码。
    4. 在“Azure 资源管理器根证书文件”框中，输入（或浏览到）证书文件的位置。
    5. 单击“下一步”。

    ![应用服务安装程序][9]

9. 对于三个证书文件框的每一个框，单击“浏览”并导航到相应的证书文件。 必须为每个证书提供密码。 这些证书是在[创建所需证书步骤](azure-stack-app-service-before-you-get-started.md#get-certificates)中创建的。 输入所有信息后，单击“下一步”。

    | Box | 证书文件名示例 |
    | --- | --- |
    | **应用服务默认 SSL 证书文件** | \_.appservice.local.AzureStack.external.pfx |
    | **应用服务 API SSL 证书文件** | api.appservice.local.AzureStack.external.pfx |
    | **应用服务发布者 SSL 证书文件** | ftp.appservice.local.AzureStack.external.pfx |

    如果在创建证书时使用了不同域后缀，证书文件名不要使用 *local.AzureStack.external*， 而是改用自定义域信息。

    ![应用服务安装程序][10]

10. 为用于托管应用服务资源提供程序数据库的服务器实例输入 SQL Server 详细信息，然后单击“下一步”。 安装程序将验证 SQL 连接属性。

    > [!NOTE]
    > 安装程序将尝试测试连接到 SQl Server，然后再继续。  但是如果你已选择要在现有虚拟网络中部署，安装程序可能无法连接到 SQL Server，并询问您是否要继续显示的警告。  请验证 SQL Server 信息并继续它们是否正确。
    >
    >

    ![应用服务安装程序][11]

11. 查看角色实例和 SKU 选项。 默认值填充实例和 ASDK 部署中的每个角色的最小 SKU 的最小数。 将提供 vCPU 和内存要求摘要以帮助你规划部署。 完成选择后，单击“下一步”。

    > [!NOTE]
    > 对于生产部署，请按照 [Azure Stack 中 Azure 应用服务服务器角色的容量规划](azure-stack-app-service-capacity-planning.md)中的指导进行操作。
    >
    >

    | 角色 | 最小实例数 | 最小 SKU | 说明 |
    | --- | --- | --- | --- |
    | 控制器 | 1 | Standard_A1 -（1 个 vCPU，1792 MB） | 管理和维护应用服务云的运行状况。 |
    | 管理 | 1 | Standard_A2 -（2 个 vCPU，3584 MB） | 管理应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员门户、租户门户、Functions 门户）和数据服务。 若要支持故障转移，请将建议的实例数增加到 2 个。 |
    | 发布者 | 1 | Standard_A1 -（1 个 vCPU，1792 MB） | 通过 FTP 和 Web 部署发布内容。 |
    | FrontEnd | 1 | Standard_A1 -（1 个 vCPU，1792 MB） | 将请求路由到应用服务应用程序。 |
    | 共享辅助角色 | 1 | Standard_A1 -（1 个 vCPU，1792 MB） | 托管 Web 应用程序或 API 应用程序和 Azure Functions 应用。 建议添加更多实例。 作为操作员，可以定义产品/服务，并选择任何 SKU 层。 这些层必须至少具有一个 vCPU。 |

    ![应用服务安装程序][13]

    > [!NOTE]
    > **Windows Server 2016 Core 不用于 Azure 堆栈上的 Azure App Service 是受支持的平台映像。请勿在生产部署中使用评估映像。**

12. 在**选择平台映像**框中，选择你部署 Windows Server 2016 的虚拟机映像从 App Service 云计算资源提供程序中可用的映像。 单击“下一步”。

13. 在下一页上：
     1. 输入“辅助角色”虚拟机管理员用户名和密码。
     2. 输入“其他角色”虚拟机管理员用户名和密码。
     3. 单击“下一步”。

    ![应用服务安装程序][15]    

14. 在摘要页上：
    1. 验证所做的选择。 若要进行更改，请使用“上一步”按钮访问前面的页。
    2. 如果配置正确，则选中复选框。
    3. 若要开始部署，请单击“下一步”。

    ![应用服务安装程序][16]

15. 在下一页上：
    1. 跟踪安装进度。 Azure Stack 上的应用服务基于默认选择部署大约需要 60 分钟。
    2. 安装程序成功完成后，单击“退出”。

    ![应用服务安装程序][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>验证 Azure Stack 上的应用服务安装

1. 在 Azure Stack 管理员门户中，转到“管理 - 应用服务”。

2. 在状态下的概述中，检查“状态”是否显示“所有角色已就绪”。

    ![应用服务管理](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>体验版 Azure Stack 上的应用服务

部署并注册应用服务资源提供程序后，对其进行测试以确保用户可以部署 Web 应用和 API 应用。

> [!NOTE]
> 需要创建在计划中具有 Microsoft.Web 命名空间的产品/服务。 然后，需要具有订阅此产品/服务的租户订阅。 有关详细信息，请参阅[创建产品/服务](azure-stack-create-offer.md)和[创建计划](azure-stack-create-plan.md)。
>
*必须*具有租户订阅，才能创建使用 Azure Stack 上的应用服务的应用程序。 服务管理员只能在管理员门户中完成的功能与应用服务的资源提供程序管理相关。 这些功能包括添加容量、配置部署源以及添加辅助角色层和 SKU。
>
若要创建 Web 应用、API 应用和 Azure Functions 应用，必须使用租户门户并具有租户订阅。

1. 在 Azure Stack 租户门户中，单击“新建” > “Web + 移动” > “Web 应用”。

2. 在“Web 应用”边栏选项卡上的“Web 应用”框中键入名称。

3. 在“资源组”下单击“新建”。 在“资源组”框中键入名称。

4. 单击“应用服务计划/位置” > “新建”。

5. 在“应用服务计划”边栏选项卡上的“应用服务计划”框中键入名称。

6. 单击“定价层” > “免费共享”或“共享共享” > “选择” > “确定” > “创建”。

7. 在不到一分钟之内，新的 Web 应用的磁贴将显示在仪表板上。 单击该磁贴。

8. 在“Web 应用”边栏选项卡上，单击“浏览”查看此应用的默认网站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、DNN 或 Django 网站（可选）

1. 在 Azure Stack 租户门户中，单击 **+**，转到 Azure Marketplace，部署 Django 网站并等待成功完成。 Django Web 平台使用基于文件系统的数据库。 它不需要任何其他资源提供程序，如 SQL 或 MySQL。

2. 如果还部署了 MySQL 资源提供程序，则可以从 Marketplace 部署 WordPress 网站。 当系统提示输入数据库参数时，请将用户名输入为 *User1@Server1*（使用所选的用户名和服务器名称）。

3. 如果还部署了 SQL Server 资源提供程序，则可以从 Marketplace 部署 DNN 网站。 当系统提示输入数据库参数时，请在运行 SQL Server 的计算机中选择已连接到资源提供程序的数据库。

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
