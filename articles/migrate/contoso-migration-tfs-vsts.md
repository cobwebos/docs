---
title: 将 Team Foundation Server 部署重构到 Azure 中的 Visual Studio Team Services (VSTS) | Microsoft Docs
description: 了解 Contoso 如何通过将本地 TFS 部署迁移到 Azure 中的 Visual Studio Team Services (VSTS) 来重构该部署。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 6d1d90ff0f9a49d3db9f4dc8894c9837942658f0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214993"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-visual-studio-team-services-vsts"></a>Contoso 迁移：将 Team Foundation Server 部署重构到 Visual Studio Team Services (VSTS)

本文展示了 Contoso 如何通过将本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Visual Studio Team Services (VSTS) 来重构该部署。 Contoso 的开发团队在过去五年中一直使用 TFS 进行团队协作和源代码管理。 现在，他们希望迁移到基于云的解决方案来执行开发和测试工作以及进行源代码管理。 在他们迁移到 DevOps 模型以及开发新的云本机应用时，VSTS 将发挥重要作用。

本文档是展示虚构公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云的一系列文章中的第十一篇。 该系列包括背景信息和说明如何设置迁移基础结构及运行不同类型的迁移的方案。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有的 Contoso 迁移方案共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 公司使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [Azure 数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：重新托管到 Azure VM 和 SQL 托管实例](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure。 他们使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用 Web VM，使用 [Azure 数据库迁移](https://docs.microsoft.com/azure/dms/dms-overview)服务将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：重新托管到 Azure VM](contoso-migration-rehost-vm.md) | 展示 Contoso 如何使用 Site Recovery 服务将其 SmartHotel 迁移到 Azure IaaS VM。
[文章 6：重新托管到 Azure VM 和 SQL Server 可用性组](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 公司使用 Site Recovery 迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到 SQL Server 可用性组。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Azure Site Recovery 将其 osTicket Linux 应用迁移到 Azure IaaS VM。
[文章 8：将 Linux 应用重新托管到 Azure VM 和 Azure MySQL 服务器](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何迁移 osTicket Linux 应用。 他们使用 Site Recovery 进行 VM 迁移，使用 MySQL Workbench 迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：将应用重构到 Azure Web 应用和 Azure SQL 数据库](contoso-migration-refactor-web-app-sql.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到基于 Azure 容器的 Web 应用，并将应用数据库迁移到 Azure SQL Server。 | 可用
[文章 10：将 Linux 应用重构到 Azure 应用服务和 Azure MySQL 服务器](contoso-migration-refactor-linux-app-service-mysql.md) | 展示 Contoso 如何使用 PHP 7.0 Docker 容器将 Linux 应用 osTicket 迁移到 Azure 应用服务。 部署的代码库迁移到 GitHub。 应用数据库迁移到 Azure MySQL。 | 可用
文章 11：在 VSTS 中重构 TFS 部署 | 将开发应用 TFS 迁移到 Azure 中的 VSTS | 本文
[文章 12：基于 Azure 容器和 Azure SQL 数据库重构应用](contoso-migration-rearchitect-container-sql.md) | 展示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器，将应用数据库重新架构到 Azure SQL 数据库中。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | 展示 Contoso 如何使用一系列 Azure 功能和服务（包括应用服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成其 SmartHotel 应用。 | 可用


## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务团队紧密合作来确定将来的目标。 业务合作伙伴不太关心开发工具和技术，但他们已经抓住了以下要点：

- **软件**：不管核心业务如何，所有公司现在都是软件公司，包括 Contoso。 业务领导关心 IT 如何借助为用户运用的新工作实践以及为客户提供服务的经验来帮助他们领导公司。
- **效率**：Contoso 需要为开发人员和用户简化流程并摒弃不必要的过程。 这将使他们可以更高效地按客户要求提供服务。 业务要求 IT 快速，不浪费时间或金钱。
- **敏捷性**：Contoso IT 需要比市场更快地响应业务需求并做出反应，以便在全球经济环境中获得成功。 IT 不能成为业务的拖累者。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了迁移到 VSTS 时的目标：

- 他们需要一个工具来将数据迁移到云。 一些流程需要手动执行。
- 上一年的工作项数据和历史记录必须迁移。
- 他们不想设置新的用户名和密码。 必须保留当前的所有系统分配。
- 他们希望从 Team Foundation Version Control (TFVC) 迁移到 Git 来进行源代码管理。
- 切换到 Git 将是一个“尖端迁移”，它仅导入源代码的最新版本。 它将在停机期间进行，在此期间内，因为代码库转移，所有工作都将停止。 他们了解在迁移后将只有当前的主分支历史记录可用。
- 他们关心更改，并希望在执行完整迁移之前对更改进行测试。 他们希望在迁移到 VSTS 后仍然能够访问 TFS。
- 他们有多个集合，并且希望从仅有少数项目的集合开始，以便更好地了解流程。
- 他们了解 TFS 集合与 VSTS 帐户之间存在一对一关系，因此将存在多个 URL。 但是，这与他们当前使用的将代码库和项目相分离的模型匹配。


## <a name="proposed-architecture"></a>建议的体系结构

- Contoso 将其 TFS 项目迁移到云，不再在本地承载其项目或进行源代码管理。
- TFS 将迁移到 VSTS。
- 当前，Contoso 有一个名为 **ContosoDev**的 TFS 集合，它将迁移到名为 **contosodevmigration.visualstudio.com** 的 VSTS 帐户。
- 上一年的项目、工作项、bug 和迭代将迁移到 VSTS。
- Contoso 将利用其 Azure Active Directory，这是他们在开始进行迁移规划时在[部署 Azure 基础结构](contoso-migration-infrastructure.md)时设置的。 


![方案体系结构](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>迁移过程

Contoso 将按如下方式完成迁移进程：

1. 涉及大量的准备工作。 第一步，Contoso 需要将其 TFS 实现升级到某个受支持的级别。 他们当前运行的是 TFS 2017 Update 3，但是，若要使用数据库迁移，他们需要运行具有最新更新的受支持 2018 版本。
2. 在升级后，他们将运行 TFS 迁移工具并验证其集合。
3. 他们将构建一个准备工作集文件，并执行迁移试运行以进行测试。
4. 然后，他们将运行另一个迁移，这次是包括工作项、bug、冲刺(sprint) 和代码在内的完整迁移。
5. 在迁移后，他们将其代码从 TFVC 移动到 Git。

![迁移过程](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>方案步骤

下面是 Azure 完成迁移的步骤：

> [!div class="checklist"]
> * **步骤 1：创建 Azure 存储帐户**：在迁移过程中将使用此存储帐户。
> * **步骤 2：升级 TFS**：将部署升级到 TFS 2018 Upgrade 2。 
> * **步骤 3：验证集合**：在执行迁移准备工作时将验证 TFS 集合。
> * **步骤 4：构建准备工作文件**：使用 TFS 迁移工具创建迁移文件。 


## <a name="step-1-create-a-storage-account"></a>步骤 1：创建存储帐户

1. 在 Azure 门户中，Contoso 创建存储帐户 (**contosodevmigration**)。
2. 他们将该帐户放置在用于故障转移的次要区域“美国中部”内。 他们使用具有本地冗余存储的常规用途标准帐户。

    ![存储帐户](./media/contoso-migration-tfs-vsts/storage1.png) 


**需要更多帮助？**

- [Azure 存储简介](https://docs.microsoft.com/azure/storage/common/storage-introduction)。
- [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。


## <a name="step-2-upgrade-tfs"></a>步骤 2：升级 TFS

Contoso 将其 TFS 服务器升级到 TFS 2018 Update 2。 在开始之前：

- 下载 [TFS 2018 Update 2](https://visualstudio.microsoft.com/downloads/)
- 验证[硬件要求](https://docs.microsoft.com/tfs/server/requirements)，并仔细阅读[发行说明](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes)和[升级陷阱](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018)。

他们如下所述进行升级：

1. 为了开始升级，他们备份其 TFS 服务器（在 VMware VM 上运行）并创建 VMware 快照。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. TFS 安装程序启动，选择安装位置。 安装程序需要访问 Internet。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. 在安装完成后，服务器配置向导启动。

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. 在验证之后，向导完成升级。

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. Contoso 通过对项目、工作项和代码进行复查来验证 TFS 安装。

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> 某些 TFS 升级在升级完成后需要运行“配置功能”向导。 [了解详细信息](https://docs.microsoft.com/vsts/work/customize/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts)。

**需要更多帮助？**

了解[升级 TFS](https://docs.microsoft.com/tfs/server/upgrade/get-started)。

## <a name="step-3-validate-the-tfs-collection"></a>步骤 3：验证 TFS 集合

Contoso 在迁移之前针对 ContosoDev 集合数据库运行 TFS 迁移工具来对其进行验证。

1. Contoso 下载并解压缩 [TFS 迁移工具](https://www.microsoft.com/download/details.aspx?id=54274)。 务必要下载适用于正在运行的 TFS 更新的版本。 可以在管理控制台中检查版本。

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. 运行该工具并通过指定团队项目集合的 URL 来执行验证。

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. 该工具显示一个错误。

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. 找到位于 **Logs** 文件夹中的日志文件，恰好在工具位置之前。 对于每个主要验证都会生成一个日志文件。 **TfsMigration.log** 包含主要信息。

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Contoso 找到了与标识相关的此条目。

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Contoso 在命令行上运行 **TfsMigration validate /help**，并发现好像需要执行命令 **/tenantDomainName** 来验证标识。

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. 再次运行验证命令，并包括此值以及 Azure AD 名称：**TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**。

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. 此时将出现 Azure AD 登录屏幕，输入全局管理员用户的凭据。

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. 验证通过，并通过工具进行确认。

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>步骤 4：创建迁移文件

验证完成后，Contoso 可以使用 TFS 迁移工具来生成迁移文件。

1. 在工具中运行准备步骤。

    **TfsMigrator prepare /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![准备](./media/contoso-migration-tfs-vsts/prep1.png)

    准备步骤执行以下操作：
    - 扫描集合以查找所有用户的列表并填充标识映射日志 (**IdentityMapLog.csv**])
    - 准备到 Azure Active Directory 的连接以查找每个标识的匹配项。
    - Contoso 已部署了 Azure AD 并使用 AD Connect 对其进行了同步，因此，准备步骤应当能够找到匹配的标识并将其标记为活动的。

2. 此时将出现 Azure AD 登录屏幕，Contoso 输入全局管理员的凭据。

    ![准备](./media/contoso-migration-tfs-vsts/prep2.png)

3. 准备步骤完成，并且该工具报告已成功生成了导入文件。

    ![准备](./media/contoso-migration-tfs-vsts/prep3.png)

4. Contoso 现在可以看到已在一个新文件夹中创建了 IdentityMapLog.csv 和 import.json 文件。

    ![准备](./media/contoso-migration-tfs-vsts/prep4.png)

5. import.json 文件提供了导入设置。 它包括诸如所需的帐户名称以及存储帐户信息等信息。 大多数字段是自动填充的。 某些字段需要用户输入。 Contoso 打开该文件，并添加要创建的 VSTS 帐户名称：**contosodevmigration**。 使用此名称，其 VSTS URL 将成为 **contosodevmigration.visualstudio.com**。

    ![准备](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > 帐户必须在迁移之前创建，并且在迁移完成后可以更改。

6. 审查标识映射日志文件，其中显示了在导入期间将被置于 VSTS 中的帐户。 

    - 活动标识指的是在导入后将成为 VSTS 中的用户的标识。
    - 在 VSTS 上，在迁移后，这些标识将获得许可并显示为帐户中的用户。
    - 这些标识在文件中的 **Expected Import Status** 列中被标记为 **Active**。

    ![准备](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-vsts"></a>步骤 5：迁移到 VSTS

准备就绪后，Contoso 现在可以集中精力执行迁移。 运行迁移后，他们将从使用 TFVC 转变为使用 Git 进行版本控制。

在开始之前，Contoso 与开发团队一起安排停机时间，并使集合脱机以便迁移。 下面是迁移流程的步骤：

1. **分离集合**：当集合已附加并处于联机状态时，集合的标识数据位于 TFS 服务器配置数据库中。 当集合从 TFS 服务器分离时，它获取该标识数据的副本，并将其与集合打包在一起进行传输。 如果没有该数据，则导入流程中有关标识部分的步骤无法执行。 建议使集合保持分离状态，直到导入完成，因为无法导入在导入期间发生的更改。
2. **生成备份**：迁移流程的下一步骤是生成可以导入到 VSTS 中的备份。 数据层应用程序组件包 (DACPAC) 是一项 SQL Server 功能，它允许将数据库更改打包到单个文件中并将其部署到其他 SQL 实例。 它还可以直接还原到 VSTS，因此可用作使集合数据进入云中的打包方法。 Contoso 将使用 SqlPackage.exe 工具来生成 DACPAC。 此工具包含在 SQL Server Data Tools 中。
3. **上传到存储**：创建 DACPAC 后，将其上传到 Azure 存储。 在上传后，获取一个共享访问签名 (SAS) 以允许 TFS 迁移工具访问存储。
4. **填写导入文件**：Contoso 然后可以在导入文件中填写缺少的字段，包括 DACPAC 设置。 首先，他们将指定在执行完整迁移之前是否要执行**试运行**导入来检查是否一切都正常工作。
5. **试运行**：试运行导入可以帮助测试集合迁移。 试运行的生存期有限，在生产迁移运行之前会被删除。 它们在设定的时间后会自动删除。 导入完成后收到的成功电子邮件中包括了有关何时将删除试运行的说明。 请注意并相应地制定计划。
6. **完成生产迁移**：完成试运行迁移后，Contoso 通过更新 import.json 并再次运行导入来执行最终迁移。



### <a name="detach-the-collection"></a>分离集合

在开始之前，Contoso 在分离之前创建一个本地 SQL Server 备份和 TFS 服务器的 VMware 快照。

1.  在 TFS 管理控制台中，选择要分离的集合 (**ContosoDev**)。

    ![迁移](./media/contoso-migration-tfs-vsts/migrate1.png)

2. 在“常规”中，选择“分离集合”。

    ![迁移](./media/contoso-migration-tfs-vsts/migrate2.png)

3. 在“分离团队项目集合”向导 >“维护消息”中，为可能尝试连接到集合中的项目的用户提供一条消息。

    ![迁移](./media/contoso-migration-tfs-vsts/migrate3.png)

4. 在“分离进度”中，监视进度并在进程完成后单击“下一步”。

    ![迁移](./media/contoso-migration-tfs-vsts/migrate4.png)

5. 在“就绪检查”中，当检查完成后，单击“分离”。

    ![迁移](./media/contoso-migration-tfs-vsts/migrate5.png)

6. 单击“关闭”以完成。

    ![迁移](./media/contoso-migration-tfs-vsts/migrate6.png)

6. TFS 管理控制台中将不再引用该集合。

    ![迁移](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>生成 DACPAC

Contoso 为导入到 VSTS 中创建备份 (DACPAC)。

- 使用 SQL Server Data Tools 中的 SqlPackage.exe 创建 DACPAC。 随 SQL Server Data Tools 安装了 SqlPackage.exe 的多个版本，分别位于名为 120、130 和 140 等等的文件夹下。 请务必使用正确的版本来准备 DACPAC。
- TFS 2018 导入需要使用 140 文件夹或更高版本文件夹中的 SqlPackage.exe。  对于 CONTOSOTFS，此文件位于以下文件夹中：**C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**。


Contoso 如下所述生成 DACPAC：

1. 打开命令提示符并导航到 SQLPackage.exe 位置。 键入以下命令来生成 DACPAC：

    **SqlPackage.exe /sourceconnectionstring:"Data Source=SQLSERVERNAME\INSTANCENAME;Initial Catalog=Tfs_ContosoDev;Integrated Security=True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData=true /p:IgnoreUserLoginMappings=true /p:IgnorePermissions=true /p:Storage=Memory** 

    ![备份](./media/contoso-migration-tfs-vsts/backup1.png)

2. 运行该命令后，会出现以下消息。

    ![备份](./media/contoso-migration-tfs-vsts/backup2.png)

3. 验证 DACPACfile 的属性

    ![备份](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>将文件更新到存储

创建 DACPAC 之后，Contoso 将其上传到 Azure 存储中。

1. [下载并安装](https://azure.microsoft.com/features/storage-explorer/) Azure 存储资源管理器。

    ![上载](./media/contoso-migration-tfs-vsts/backup5.png)

4. 连接到其订阅并找到他们为迁移创建的存储帐户 (**contosodevmigration**)。 创建一个新的 blob 容器 **vstsmigration**。

    ![上载](./media/contoso-migration-tfs-vsts/backup6.png)

5. 将要上传的 DACPAC 文件指定为块 blob。

    ![上载](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. 上传文件后，单击文件名 >“生成 SAS”。 在存储帐户下展开 blob 容器，选择包含导入文件的容器，然后单击“获取共享访问签名”。

    ![上载](./media/contoso-migration-tfs-vsts/backup8.png)

8. 接受默认设置，然后单击“创建”。 这会将访问权限启用 24 小时。

    ![上载](./media/contoso-migration-tfs-vsts/backup9.png)

9. 复制共享访问签名 URL，以便供 TFS 迁移工具使用。

    ![上载](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> 迁移必须在允许的时间范围到期前进行，否则权限将失效。
> 不要从 Azure 门户生成 SAS 密钥。 通过此方式生成的密钥以帐户为作用域，不能用于导入。

### <a name="fill-in-the-import-settings"></a>填写导入设置

之前，Contoso 已经部分填写了导入规范文件 (import.json)。 现在，他们需要添加剩余的设置。

他们打开 import.json 文件，并填写以下字段：•   Location：前面生成的 SAS 密钥的位置。
•   Dacpac：将名称设置为你上传到存储帐户的 DACPAC 文件的名称。 包括“.dacpac”扩展名。
• ImportType：目前设置为 DryRun。


![导入设置](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>试运行迁移

Contoso 首先试运行迁移，确保一切按预期正常工作

1. 打开命令提示符，并定位到 TfsMigration 位置 (C:\TFSMigrator)。
2. 第一步，验证导入文件。 他们希望确保文件的格式设置正确，并且 SAS 密钥有效。

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

3. 验证返回一个错误，指出 SAS 密钥需要更长的到期时间。

    ![试运行](./media/contoso-migration-tfs-vsts/test1.png)

3. 使用 Azure 存储资源管理器创建一个新的 SAS 密钥，将其到期时间设置为 7 天。

    ![试运行](./media/contoso-migration-tfs-vsts/test2.png)

3. 更新 import.json 文件并再次运行验证。 这次，它成功完成。

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json /validateonly**

    ![试运行](./media/contoso-migration-tfs-vsts/test3.png)
    
7. 开始试运行：

    **TfsMigrator import /importFile:C:\TFSMigrator\import.json**

8. 此时会发出一条消息，用以确认迁移。 记下临时数据在试运行后将保留的时长。

    ![试运行](./media/contoso-migration-tfs-vsts/test4.png)

9. 此时将出现 Azure AD 登录屏幕，并且应当完成 Contoso 管理员登录。

    ![试运行](./media/contoso-migration-tfs-vsts/test5.png)

10. 一条消息显示了有关导入的信息。

    ![试运行](./media/contoso-migration-tfs-vsts/test6.png)

11. 大约 15 分钟后，Contoso 浏览到该 URL 并且看到以下信息：

     ![试运行](./media/contoso-migration-tfs-vsts/test7.png)

12. 在迁移完成后，Contoso 开发主管登录到 VSTS 来检查试运行是否正常工作。 在身份验证后，VSTS 需要一些详细信息来确认帐户。

    ![试运行](./media/contoso-migration-tfs-vsts/test8.png)

13. 在 VSTS 中，开发主管可以看到项目已迁移到 VSTS。 有一则通知指出帐户将在 15 天后删除。

    ![试运行](./media/contoso-migration-tfs-vsts/test9.png)

14. 开发主管打开其中一个项目，然后打开“工作项” > “分配给我的”。 这将显示工作项数据已随他的标识一起迁移。

    ![试运行](./media/contoso-migration-tfs-vsts/test10.png)

15. 他还检查其他项目和代码，以确认源代码和历史记录是否已迁移。

    ![试运行](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>运行生产迁移

试运行完成后，Contoso 继续进行生产迁移。 他们删除试运行，更新导入设置，并再次运行导入。

1. 在 VSTS 门户中，删除试运行帐户。
2. 更新 import.json 文件以将 **ImportType** 设置为 **ProductionRun**。

    ![生产](./media/contoso-migration-tfs-vsts/full1.png)

3. 像试运行时那样开始迁移：**TfsMigrator import /importFile:C:\TFSMigrator\import.json**。
4. 此时会显示一条消息，用以确认迁移，并且提醒用户，数据最多可以在一个作为临时区域的安全位置存储七天。

    ![生产](./media/contoso-migration-tfs-vsts/full2.png)

5. 在 Azure AD 登录屏幕中，Contoso 指定一个 Contoso 管理员登录名。

    ![生产](./media/contoso-migration-tfs-vsts/full3.png)

6. 一条消息显示了有关导入的信息。

    ![生产](./media/contoso-migration-tfs-vsts/full4.png)

7. 大约 15 分钟后，Contoso 浏览到该 URL 并且看到以下信息：

    ![生产](./media/contoso-migration-tfs-vsts/full5.png)

8. 在迁移完成后，Contoso 开发主管登录到 VSTS 来检查迁移是否正常工作。 登录后，他可以看到项目已迁移。

    ![生产](./media/contoso-migration-tfs-vsts/full6.png)

8. 开发主管打开其中一个项目，然后打开“工作项” > “分配给我的”。 这将显示工作项数据已随他的标识一起迁移。

    ![生产](./media/contoso-migration-tfs-vsts/full7.png)

9. 他检查其他工作项数据以进行确认。

    ![生产](./media/contoso-migration-tfs-vsts/full8.png)

15. 他还检查其他项目和代码，以确认源代码和历史记录是否已迁移。

    ![生产](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>从 TFVC 转移到 GIT 来进行源代码管理

迁移完成后，Contoso 希望从 TFVC 转移到 Git 来进行源代码管理。 他们需要将当前在其 VSTS 帐户中的源代码导入为同一帐户中的 Git 存储库。

1. 在 VSTS 门户中，打开其中一个 TFVC 存储库 (**$/PolicyConnect**) 并审查它。

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. 单击“源”下拉列表 >“导入”。

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. 在“源类型”中选择“TFVC”，并指定存储库的路径。 他们决定不迁移历史记录。

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > 由于 TFVC 和 Git 存储版本控制信息的方式有所不同，建议不要迁移历史记录。 这是 Microsoft 在将 Windows 和其他产品从集中化版本控制迁移到 Git 时采取的方式。

4. 导入后，Contoso 对代码进行审查。

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. 为第二个存储库 (**$/SmartHotelContainer**) 重复此过程。

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. 在审查源代码后，开发主管确认到 VSTS 的迁移完成。 VSTS 现在成为迁移中涉及的团队中的所有开发成果的来源。

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**需要更多帮助？**

[详细了解](https://docs.microsoft.com/vsts/git/import-from-tfvc?view=vsts)如何从 TFVC 进行导入。

##  <a name="clean-up-after-migration"></a>迁移后的清理

迁移完成后，Contoso 需要执行以下操作：

- 查看[导入后](https://docs.microsoft.com/vsts/articles/migration-post-import?view=vsts)一文来了解有关更多导入活动的信息。
- 删除 TFVC 存储库，或者将它们置于只读模式。 代码库不能使用，但用户在需要其历史记录时可以参考它们。

## <a name="next-steps"></a>后续步骤

Contoso 将需要为相关团队成员提供 VSTS 和 Git 培训。



