---
title: 高度可用配置中部署 Azure Stack 应用服务 |Microsoft Docs
description: 了解如何部署 Azure Stack 使用了高可用性配置中的应用服务。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/23/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 1c105548f19994c4ca0ce161eedcfe11736864c7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370017"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>高度可用配置中部署应用服务

本文介绍如何使用 Azure Stack marketplace 项高度可用配置中部署 Azure Stack 的应用服务。 除了可用 marketplace 项，此解决方案还使用[appservice 的文件共享-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板。 此模板会自动创建用于托管应用服务资源提供程序的高可用性基础结构。 然后此高度可用的 VM 基础结构上安装应用服务。 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>部署高度可用的应用服务基础结构 Vm
[Appservice 的文件共享-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板可简化高度可用配置中的应用服务部署。 应在默认提供商订阅中部署它。 

在 Azure Stack 中创建自定义资源时，该模板将创建：
- 虚拟网络和所需的子网。
- 为文件服务器、 SQL Server 和 Active Directory 域服务 (AD DS) 子网的网络安全组。
- VM 磁盘和群集云见证的存储帐户。
- 使用专用 IP 的 SQL Vm 的一个内部负载均衡器绑定到 SQL AlwaysOn 侦听器。
- AD DS、 文件服务器群集和 SQL 群集的三个可用性集。
- 两个节点的 SQL 群集。
- 双节点文件服务器群集。
- 两个域控制器。

### <a name="required-azure-stack-marketplace-items"></a>所需的 Azure Stack marketplace 项
使用此模板之前，请确保以下[Azure Stack marketplace 项](azure-stack-marketplace-azure-items.md)均位于 Azure Stack 实例：

- Windows Server 2016 Datacenter Core 映像 （适用于 AD DS 和文件服务器 Vm）
- Windows Server 2016 (Enterprise) 上的 SQL Server 2016 SP2
- 最新 SQL IaaS 扩展 
- 最新的 PowerShell Desired State Configuration 扩展 

> [!TIP]
> 审阅[模板的自述文件](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha)模板要求和默认值的其他详细信息的 GitHub 上。 

### <a name="deploy-the-app-service-infrastructure"></a>部署应用服务基础结构
使用在本部分中的步骤创建一个自定义部署使用**appservice 的文件共享-sqlserver-ha** Azure Stack 快速入门模板。

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 选择**\+** **创建资源** > **自定义**，，然后**模板部署**。

   ![自定义模板部署](media/app-service-deploy-ha/1.png)


3. 上**自定义部署**边栏选项卡，选择**编辑模板** > **快速入门模板**，然后使用下拉列表的可用自定义模板选择**appservice 的文件共享-sqlserver-ha**模板，单击**确定**，然后**保存**。

   ![选择应用服务-文件共享-sqlserver-ha 快速入门模板](media/app-service-deploy-ha/2.png)

4. 上**自定义部署**边栏选项卡，选择**编辑参数**和向下滚动到查看的默认模板值。 修改这些值，根据需要提供所有所需的参数信息，然后单击**确定**。<br><br> 至少为 ADMINPASSWORD、 FILESHAREOWNERPASSWORD、 FILESHAREUSERPASSWORD、 SQLSERVERSERVICEACCOUNTPASSWORD 和 SQLLOGINPASSWORD 参数中提供复杂的密码。
    
   ![编辑自定义部署参数](media/app-service-deploy-ha/3.png)

5. 上**自定义部署**边栏选项卡，确保**默认提供商订阅**被选为要使用然后创建新的资源组，或选择现有资源组，自定义的订阅部署。<br><br> 接下来，选择资源组位置 (**本地**对于 ASDK 安装)，然后单击**创建**。 模板部署开始之前，将会验证自定义部署设置。

    ![创建自定义部署](media/app-service-deploy-ha/4.png)

6. 在管理门户中，选择**资源组**然后的资源组的名称创建自定义部署的和 (**应用服务-ha**在此示例中)。 查看以确保所有部署已成功都完成部署的状态。

   > [!NOTE]
   > 模板部署需要约一小时才能完成。

   [![](media/app-service-deploy-ha/5-sm.png "查看模板部署状态")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>记录模板输出
该模板后部署成功完成，记录输出模板部署。 你将需要运行应用服务安装程序时提供此信息。 

请务必记下每个输出值：
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

请按照下列步骤以发现模板输出值：

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 在管理门户中，选择**资源组**然后的资源组的名称创建自定义部署的和 (**应用服务-ha**在此示例中)。 

3. 单击**部署**，然后选择**Microsoft.Template**。

    ![Microsoft.Template 部署](media/app-service-deploy-ha/6.png)

4. 选择后**Microsoft.Template**部署中，选择**输出**并记录模板参数输出。 部署应用服务时，将需要此信息。

    ![参数输出](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>高度可用配置中部署应用服务
按照本部分将基于高度可用配置中部署 Azure Stack 的应用服务中的步骤[appservice 的文件共享-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板。 

安装应用服务资源提供程序后，可以将其包括在套餐和计划中。 然后，用户可以订阅以获取服务并开始创建应用程序。

> [!IMPORTANT]
> 运行资源提供程序安装程序之前，请确保已阅读发行说明，其伴随每个应用服务版本，若要了解有关新功能、 修复程序和任何已知的问题，这可能会影响你的部署。

### <a name="prerequisites"></a>必备组件
在可以运行应用服务安装程序之前，几个步骤所需中所述[开始使用应用服务在 Azure Stack 文章之前](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> 并非所有的步骤中所述在开始之前，因为模板部署会为你配置基础结构 Vm 所需的文章。 

- [下载应用服务安装程序和帮助器脚本](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts)。
- [下载最新的自定义脚本扩展到 Azure Stack marketplace](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace)。
- [生成所需的证书](azure-stack-app-service-before-you-get-started.md#get-certificates)。
- 创建基于 Azure Stack 所选择的标识提供程序 ID 应用程序。 可以针对任何一个允许应用程序 ID [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application)或[Active Directory 联合身份验证服务](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application)并记录应用程序 id。
- 请确保您具有 Windows Server 2016 Datacenter 映像添加到 Azure Stack marketplace。 这是应用服务安装所必需的。

### <a name="deploy-app-service-in-highly-available-configuration"></a>高度可用配置中部署应用服务
安装应用服务资源提供程序至少需要一小时。 所需时长取决于部署的角色实例数。 部署期间，安装程序运行以下任务：

- 在指定的 Azure Stack 存储帐户中创建 blob 容器。
- 为应用服务中创建 DNS 区域和条目。
- 注册应用服务资源提供程序。
- 注册应用服务库项。

若要部署应用服务资源提供程序，请执行以下步骤：

1. 运行以前下载的应用服务安装程序 (**appservice.exe**) 以管理员身份从可以访问 Azure Stack 管理员 Azure 资源管理终结点的计算机。

2. 选择“部署应用服务或升级到最新版本”。

    ![部署或升级](media/app-service-deploy-ha/01.png)

3. 接受 Microsoft 许可条款，然后单击**下一步**。

    ![Microsoft 许可条款](media/app-service-deploy-ha/02.png)

4. 接受非 Microsoft 许可条款，然后单击**下一步**。

    ![非 Microsoft 许可条款](media/app-service-deploy-ha/03.png)

5. 提供应用服务在 Azure Stack 环境的云终结点配置。

    ![应用服务云终结点配置](media/app-service-deploy-ha/04.png)

6. **连接**到 Azure Stack 订阅，用于安装和选择的位置。 

    ![连接到 Azure Stack 订阅](media/app-service-deploy-ha/05.png)

7. 选择**使用现有的 VNet 和子网**并**资源组名称**用于部署高度可用的模板的资源组。<br><br>接下来，选择模板部署的一部分创建的虚拟网络，然后从下拉列表选项中选择相应的角色子网。 

    ![Vnet 所选内容](media/app-service-deploy-ha/06.png)

8. 提供以前记录的模板输出的文件共享路径和文件共享所有者参数信息。 完成后，单击**下一步**。

    ![文件共享输出信息](media/app-service-deploy-ha/07.png)

9. 因为用于安装应用服务的计算机不在与用于托管应用服务文件共享的文件服务器相同的 VNet 上，将无法解析名称。 **这是预期的行为**。<br><br>验证输入文件共享 UNC 路径和帐户信息的信息是否正确，然后按**是**警报对话框以继续安装应用服务上。

    ![预期的错误对话框](media/app-service-deploy-ha/08.png)

    如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理门户中 WorkersNsg 并添加出站安全规则具有以下属性：
    - 源：任意
    - 源端口范围：*
    - 目标：IP 地址
    - 目标 IP 地址范围：文件服务器的 IP 范围
    - 目标端口范围：445
    - 协议：TCP
    - 操作：允许
    - 优先级：700
    - 姓名：Outbound_Allow_SMB445

10. 提供标识应用程序 ID 的路径和标识证书的密码，然后单击**下一步**:
    - 标识应用程序证书 (采用格式**sso.appservice.local.azurestack.external.pfx**)
    - Azure 资源管理器根证书 (**AzureStackCertificationAuthority.cer**)

    ![ID 的应用程序证书和根证书](media/app-service-deploy-ha/008.png)

10. 接下来，提供以下证书所需的其余信息并单击**下一步**:
    - 默认的 Azure Stack SSL 证书 (采用格式 **_.appservice.local.azurestack.external.pfx**)
    - API SSL 证书 (采用格式**api.appservice.local.azurestack.external.pfx**)
    - 发布服务器证书 (中的窗体**ftp.appservice.local.azurestack.external.pfx**) 

    ![其他配置证书](media/app-service-deploy-ha/09.png)

11. 提供使用高可用性模板部署输出中的 SQL Server 连接信息的 SQL Server 连接信息：

    ![SQL Server 连接信息](media/app-service-deploy-ha/10.png)

12. 因为用于安装应用服务的计算机不在与 SQL server 用于托管应用服务数据库相同的 VNet 上，将无法解析名称。  **这是预期的行为**。<br><br>验证输入的 SQL Server 名称和帐户信息的信息是否正确，然后按**是**才能继续应用服务安装。 单击“下一步”。

    ![SQL Server 连接信息](media/app-service-deploy-ha/11.png)

13. 接受默认角色配置值或更改为建议的值，然后单击**下一步**。<br><br>我们建议为高可用性配置，如下所示更改应用服务基础结构角色实例的默认值：

    |角色|默认|高可用性建议|
    |-----|-----|-----|
    |控制器角色|2|2|
    |管理角色|第|3|
    |发布服务器角色|第|3|
    |前端角色|第|3|
    |共享的辅助角色|第|10|
    |     |     |     |

    ![基础结构角色实例的值](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 从默认值更改为那些建议这样 tutoral 增加了在安装应用程序服务的硬件要求。 需要 26 个内核的总数和 46,592 MB 的 RAM 来支持的 15 Vm 的建议的 21 Vm 而不是默认 18 个内核和 32,256 MB 的 RAM 时。

14. 选择要用于安装应用服务基础结构 Vm 平台映像，然后单击**下一步**:

    ![平台映像选择](media/app-service-deploy-ha/13.png)

15. 提供应用服务基础结构角色凭据信息，以使用单击**下一步**:

    ![基础结构角色凭据](media/app-service-deploy-ha/14.png)

16. 查看信息以用于部署应用服务，然后单击**下一步**以开始部署。 

    ![查看安装摘要](media/app-service-deploy-ha/15.png)

17. 查看应用服务部署进度。 这可能需要一小时，具体取决于特定部署配置和硬件。 安装程序已成功完成后，选择**退出**。

    ![设置已完成](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>后续步骤

[横向扩展应用服务](azure-stack-app-service-add-worker-roles.md)。 您可能需要添加更多应用服务基础结构角色辅助角色以满足您的环境中的预期的应用程序需求。 默认情况下，Azure Stack 上的应用服务支持免费和共享辅助角色层。 若要添加其他辅助角色层，需添加更多的辅助角色。

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)。 支持按需部署从 GitHub、 BitBucket、 OneDrive 和 DropBox 等的多个源代码管理提供程序还需要其他配置。

[应用服务备份](app-service-back-up.md)。 后已成功部署和配置应用服务，应确保为灾难恢复所必需的所有组件进行都备份以便防止数据丢失，并在恢复操作期间避免不必要的服务停机时间。