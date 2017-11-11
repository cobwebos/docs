---
title: "部署应用程序服务： Azure 堆栈 |Microsoft 文档"
description: "部署 Azure 堆栈中的应用程序服务的详细的指南"
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
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: dba3da6f1aaf47c1b518fe0dba7ea22ae555c8c0
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>添加到 Azure 堆栈的 App Service 资源提供程序

作为 Azure 堆栈云操作员，你可以使你的用户能够创建 web 角色和 API 应用程序。 若要执行此操作，必须首先添加[App Service 资源提供程序](azure-stack-app-service-overview.md)到 Azure 堆栈部署本文中所述。 安装 App Service 资源提供程序后，你可以将其包含在你的产品/服务和计划。 然后，用户可以订阅以获取服务并开始创建应用程序。

> [!IMPORTANT]
> 在之前运行安装程序，请确保您已按照中的指导[开始之前](azure-stack-app-service-before-you-get-started.md)。
> 
>



## <a name="run-the-app-service-resource-provider-installer"></a>运行应用程序服务资源提供程序安装程序

App Service 资源提供程序安装到 Azure 堆栈环境可以花费一个小时的时间。 在此过程中，安装程序将：

* 指定 Azure 堆栈存储帐户中创建 blob 容器。
* 为 App Service 中创建 DNS 区域和条目。
* 注册应用程序服务资源提供程序。
* 注册应用程序服务库项。

若要部署 App Service 资源提供程序，请按照下列步骤：

1. 以管理员身份 (azurestack\CloudAdmin) 运行 appservice.exe。

2. 单击**Azure 堆栈云上部署 App Service**。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image01.png)

3. 查看并接受 Microsoft 软件许可条款，然后单击**下一步**。

4. 查看并接受第三方许可条款，然后单击**下一步**。

5. 请确保应用程序服务的云配置信息正确。 如果你在 Azure 堆栈开发工具包部署过程中使用的默认设置，你可以接受默认值此处。 但是，如果你自定义选项，你在部署 Azure 堆栈时，你必须编辑此窗口以反映该中的值。 例如，如果你使用域后缀 mycloud.com，你的终结点必须将更改为 management.mycloud.com。确认你的信息后，单击**下一步**。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image02.png)

6. 在下一页上：
    1. 单击**连接**按钮旁边**Azure 堆栈订阅**框。
        - 如果你使用 Azure Active Directory (Azure AD)，输入的 Azure AD 管理员帐户和你在部署 Azure 堆栈时提供的密码。 单击**登录**。
        - 如果你使用 Active Directory 联合身份验证服务 (AD FS)，提供你的管理员帐户。 例如，cloudadmin@azurestack.local。 输入你的密码，然后单击**登录**。
    2. 在**Azure 堆栈订阅**框中，选择你的订阅。
    3. 在**Azure 堆栈位置**框中，选择对应于要部署到的区域的位置。 例如，选择**本地**如果你部署到 Azure 堆栈开发工具包。
    4. 输入**资源组名称**为你的 App Service 部署。 默认情况下，它设置为**APPSERVICE\<区域\>**。
    5. 输入**存储帐户名称**你想要作为安装的一部分创建的应用程序服务。 默认情况下，它设置为**appsvclocalstor**。
    6. 单击“下一步”。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image03.png)

7. 输入你的文件共享的信息，然后单击**下一步**。 文件共享的地址必须使用完全限定域名为文件服务器，例如\\\appservicefileserver.local.cloudapp.azurestack.external\websites 或 IP 地址，例如\\\10.0.0.1\websites。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image04.png)

8. 在下一页上：
    1. 在**标识应用程序 ID**框中，输入将用于标识应用程序的 GUID。
    2. 在**标识应用程序证书文件**框中，输入 （或浏览到） 证书文件的位置。
    3. 在**标识应用程序证书密码**框中，为证书输入密码。 此密码是你记下的时使用脚本来创建证书。
    4. 在**Azure 资源管理器的根证书文件**框中，输入 （或浏览到） 证书文件的位置。
    5. 单击“下一步”。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image05.png)

9. 每三个证书文件框，请单击**浏览**并导航到适当的证书文件。 你必须为每个证书提供密码。 这些证书是在中创建那些[创建所需的证书步骤](azure-stack-app-service-deploy.md#create-the-required-certificates)。 单击**下一步**后输入的所有信息。

    | Box | 证书文件名称示例 |
    | --- | --- |
    | **应用程序服务默认 SSL 证书文件** | \_。 appservice.local.AzureStack.external.pfx |
    | **App Service API SSL 证书文件** | api.appservice.local.AzureStack.external.pfx |
    | **应用程序服务发布服务器 SSL 证书文件** | ftp.appservice.local.AzureStack.external.pfx |

    如果你在创建证书时，你可以使用不同的域后缀，不要使用你的证书文件名称*本地。AzureStack.external*。 相反，使用你的自定义域的信息。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image06.png)    

10. 输入用于承载应用程序服务资源提供程序数据库，然后单击的服务器实例的 SQL Server 详细信息**下一步**。 安装程序验证的 SQL 连接属性。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image07.png)    

11. 查看角色实例和 SKU 选项。 默认值填充实例和 ASDK 部署中的每个角色的最小 SKU 的最小数。 VCPU 和内存要求的摘要可用于帮助规划你的部署。 进行选择后，单击**下一步**。

    > [!NOTE]
    > 对于生产部署，按照中的指导[容量规划 Azure 堆栈中的 Azure App Service 服务器角色](azure-stack-app-service-capacity-planning.md)。
    > 
    >

    | 角色 | 最小实例 | 最小的 SKU | 说明 |
    | --- | --- | --- | --- |
    | 控制器 | 1 | Standard_A1-（1 vCPU、 1792 MB） | 管理和维护 App Service 云的运行状况。 |
    | 管理 | 1 | Standard_A2-（2 Vcpu、 3584 MB） | 管理的应用程序服务 Azure 资源管理器和 API 终结点、 门户 （管理员、 租户、 函数门户） 的扩展和数据服务。 若要支持故障转移，增加到 2 的建议的实例。 |
    | 发布者 | 1 | Standard_A1-（1 vCPU、 1792 MB） | 发布通过 FTP 和 web 部署的内容。 |
    | FrontEnd | 1 | Standard_A1-（1 vCPU、 1792 MB） | 将请求路由到 App Service 应用程序。 |
    | 共享工作线程 | 1 | Standard_A1-（1 vCPU、 1792 MB） | 主机 web API 应用和 Azure 函数应用。 你可能想要添加更多实例。 作为一个操作员，你可以定义您的产品，并选择任何 SKU 层。 层必须具有一个 vCPU 的最小值。 |

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core 不用于 Azure 堆栈上的 Azure App Service 是受支持的平台映像**。

12. 在**选择平台映像**框中，选择从 App Service 云计算资源提供程序中提供的那些你部署 Windows Server 2016 的虚拟机映像。 单击“下一步”。

13. 在下一页上：
     1. 输入辅助角色虚拟机管理员用户名和密码。
     2. 输入其他角色虚拟机管理员用户名和密码。
     3. 单击“下一步”。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image09.png)    

14. 在摘要页上：
    1. 验证你所做的选择。 若要进行更改，请使用**上一步**按钮访问以前的页面。
    2. 如果配置不正确，则选中的复选框。
    3. 若要开始部署，请单击**下一步**。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image10.png)    

15. 在下一页上：
    1. 跟踪安装进度。 Azure 堆栈上的 app Service 需要大约 60 分钟部署基于默认选择。
    2. 安装程序成功完成后，单击**退出**。

    ![App Service 安装程序](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>验证 Azure 堆栈安装上的应用程序服务

1. 在 Azure 堆栈管理员门户中，转到**管理的 App Service**。

2. 在下状态的概述，请检查是否，**状态**显示**所有角色都就**。

    ![应用程序服务管理](media/azure-stack-app-service-deploy/image12.png)    

## <a name="test-drive-app-service-on-azure-stack"></a>测试 Azure 堆栈上的驱动器 App Service

部署并注册应用程序服务资源提供程序后，测试它以确保用户可以部署 web 应用和 API 应用。

> [!NOTE]
> 你需要创建产品具有在计划内的 Microsoft.Web 命名空间。 然后，你需要具有订阅此产品/服务的租户订阅。 有关详细信息，请参阅[创建优惠](azure-stack-create-offer.md)和[创建计划](azure-stack-create-plan.md)。
>
你*必须*具有要创建使用 Azure 堆栈的 App Service 应用程序的租户订阅。 与 App Service 的资源提供程序管理相关的服务管理员可以在管理门户内完成的唯一功能。 这些功能包括添加容量、 配置部署源和添加辅助角色层和 Sku。
>
截至第三个 technical preview 中，创建 web、 API 和 Azure 函数应用，必须使用租户门户，并且有租户订阅。

1. 在 Azure 堆栈租户门户中，单击**新建** > **Web + 移动** > **Web 应用**。

2. 上**Web 应用**边栏选项卡中键入名称**Web 应用**框。

3. 下**资源组**，单击**新建**。 中键入一个名称**资源组**框。

4. 单击“应用服务计划/位置” > “新建”。

5. 上**App Service 计划**边栏选项卡中键入名称**App Service 计划**框。

6. 单击**定价层** > **免费共享**或**共享共享** > **选择** >  **确定** > **创建**。

7. 在下一分钟，新的 web 应用的磁贴将显示在仪表板上。 单击该磁贴。

8. 上**Web 应用**边栏选项卡，单击**浏览**若要查看此应用程序的默认网站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、 DNN 或 Django 网站 （可选）

1. 在 Azure 堆栈租户门户中，单击 **+** ，转到 Azure 应用商店、 部署 Django 网站，并等待成功完成。 Django web 平台使用文件系统基于的数据库。 它不需要任何其他资源提供程序，如 SQL 或 MySQL。

2. 如果您还部署了 MySQL 资源提供程序，你可以部署应用商店中的 WordPress 网站。 当系统提示你输入数据库参数时，输入作为用户名 *User1@Server1* 、 具有用户名称和所选的服务器名称。

3. 如果您还部署了 SQL Server 资源提供程序，你可以部署 DNN 网站从应用商店。 当系统提示你输入数据库参数时，请在运行连接到资源提供程序的 SQL Server 的计算机中选择的数据库。

## <a name="next-steps"></a>后续步骤

你还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)。

- [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
