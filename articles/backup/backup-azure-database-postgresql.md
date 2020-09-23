---
title: 备份 Azure Database for PostgreSQL
description: '了解 Azure Database for PostgreSQL 具有长期保留 (预览版的备份) '
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: ae18a5bb40d8993c82b2f3e03f231711ba3beebe
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994676"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL 具有长期保留 (预览版的备份) 

Azure 备份和 Azure 数据库服务汇集了一个企业级备份解决方案，适用于最长为10年的备份服务器 Azure Database for PostgreSQL 服务器。

除了长期保留外，该解决方案还有其他许多功能，如下所示：

- 使用 Azure Active Directory 和托管服务标识 (MSI) 身份验证的基于 RBAC 的数据库访问权限。
- 在单独的数据库级别进行客户控制的计划和按需备份。
- 数据库级还原到任何 Postgres 服务器或直接存储到 blob 存储。
- 长期保留。
- 所有操作和作业的集中监视。
- 备份存储在单独的安全域和容错域中。 因此，即使源服务器被泄露或甚至被终止，备份也在 [备份保管库](backup-vault-overview.md)中仍然是安全的。
- 使用 **pg_dump** 可以更灵活地进行还原，这样，你可以在数据库版本之间进行还原，甚至只能还原备份的一部分。

你可以独立使用此解决方案，也可以使用 Azure PostgreSQL 提供的本机备份解决方案，该解决方案可提供长达35天的保留期。 本机解决方案适用于操作恢复，例如当你想要从最新备份中恢复时。 Azure 备份解决方案可帮助满足你的符合性需求，以及更精细、更精细的备份与还原。

## <a name="support-matrix"></a>支持矩阵

|支持  |详细信息  |
|---------|---------|
|支持的部署   |  Azure Database for PostgreSQL 独立单一服务器     |
|支持的 Azure 区域 |  美国东部、美国东部2、美国中南部、美国西部 |
|支持的 Azure PostgreSQL 版本    |   9.5、9.6、10、11      |

## <a name="feature-considerations-and-limitations"></a>功能注意事项和限制

- 仅 Azure 门户支持所有操作。
- 最大数据库大小建议的限制为 400 GB。
- 不支持跨区域备份。 这意味着不能将 Azure PostgreSQL 服务器备份到其他区域中的保管库。 同样，只能将备份还原到保管库所在的同一区域中的服务器。
- 还原时仅恢复数据。 不还原 "角色"。
- 在预览版中，我们建议仅在测试环境中运行解决方案。

## <a name="backup-process"></a>备份过程

1. 此解决方案使用 **pg_dump** 对 Azure PostgreSQL 数据库进行备份。

2. 指定要备份的 Azure PostgreSQL 数据库后，该服务将验证它是否具有适当的权限集和对服务器和数据库执行备份操作的访问权限。

3. Azure 备份会在其中安装有备份扩展的辅助角色。 此扩展与 Postgres 服务器通信。

4. 此扩展包括协调器和 Azure Postgres 插件。 当协调器负责为各种操作（如配置备份、备份和还原）触发工作流时，该插件负责实际数据流。
  
5. 在所选数据库上触发配置保护后，备份服务会为协调器设置备份计划和其他策略详细信息。

6. 在计划的时间，协调器会与插件通信，并使用 **pg_dump**从 Postgres 服务器开始流式传输备份数据。

7. 此插件将数据直接发送到备份保管库，从而无需暂存位置。 使用 Microsoft 托管的密钥对数据进行加密，并将其存储在存储帐户中。

8. 数据传输完成后，协调器将用备份服务确认提交。

    ![备份过程](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>在 Azure PostgreSQL 数据库上配置备份

可以跨多个 Azure PostgreSQL 服务器配置跨多个数据库的备份。 请确保已配置了服务备份 Postgres 服务器所需的 [先决条件权限](#prerequisite-permissions-for-configure-backup-and-restore) 。

以下说明是在 Azure PostgreSQL 数据库中使用 Azure 备份配置备份的分步指南：

1. 可以通过两种方法开始执行此过程：

    1. 请参阅[备份中心](backup-center-overview.md)  ->  **概述**  ->  **备份**。

        ![中转到备份中心](./media/backup-azure-database-postgresql/backup-center.png)

        在 " **启动：配置备份**" 下，选择 " **数据源类型** " 作为 **Azure Database for PostgreSQL**。

        ![在 "启动：配置备份" 中，选择 "数据源类型"](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. 或者，可以直接执行[备份保管库](backup-vault-overview.md)  ->  **备份**。

        ![中转到备份保管库](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![选择备份保管库中的备份](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. 在 " **配置备份**" 下，选择要将 Postgres 数据库备份到的 **备份保管库** 。 如果已在保管库上下文中，则会预先填充此信息。

    ![选择 "配置备份" 中的备份保管库](./media/backup-azure-database-postgresql/configure-backup.png)

1. 选择或创建 **备份策略**。

    ![选择备份策略](./media/backup-azure-database-postgresql/backup-policy.png)

1. 选择要备份的资源或 Postgres 数据库。

    ![选择要备份的资源](./media/backup-azure-database-postgresql/select-resources.png)

1. 如果订阅位于与保管库相同的区域中，则可以在订阅中选择所有 Azure PostgreSQL 服务器的列表。 展开箭头，查看服务器内数据库的列表。

    ![选择服务器](./media/backup-azure-database-postgresql/choose-servers.png)

1. 服务对所选数据库运行这些检查，以验证保管库是否有权备份所选的 Postgres 服务器和数据库。
    1. 所有数据库的**备份就绪**状态应为 "**成功**" 才能继续。
    1. 如果出现错误，请 **修复** 错误，然后重新 **验证** 或删除所选内容中的数据库。

    ![要修复的验证错误](./media/backup-azure-database-postgresql/validation-errors.png)

1. 确认 " **查看并配置** " 下的所有详细信息，然后选择 " **配置备份** " 以提交操作。

    ![确认详细信息以查看和配置](./media/backup-azure-database-postgresql/review-and-configure.png)

1. 一旦触发， **配置备份** 操作就会创建一个备份实例。 可以在备份中心或保管库视图中的 " [备份实例](backup-center-monitor-operate.md#backup-instances) " 窗格下跟踪操作的状态。

    ![备份实例](./media/backup-azure-database-postgresql/backup-instances.png)

1. 根据策略中定义的备份计划来触发备份。 可以在 " [备份作业](backup-center-monitor-operate.md#backup-jobs)" 下跟踪作业。 目前，可以查看过去7天的作业。

    ![备份作业](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>创建备份策略

1. 请参阅 "**备份中心**  ->  **备份策略**" "  ->  **添加**"。 或者，你可以执行 "**备份保管库**  ->  **备份策略**" "  ->  **添加**"。

    ![添加备份策略](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. 输入新策略的 **名称** 。

    ![输入策略名称](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. 定义备份计划。 目前支持 **每周** 备份。 你可以计划一周中的一天或几天的备份。

    ![定义备份计划](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. 定义 **保留** 设置。 您可以添加一个或多个保留规则。 每个保留规则都假定特定备份的输入，以及这些备份的数据存储和保留期。

1. 你可以选择将备份存储在两个数据存储的其中一个 (或层) ： ** (热** 层) 或 **存档数据存储** (在预览版) 中。 可以在 **两个分层选项** 之间进行选择，以定义在两个数据存储中分层备份的时间：

    - 如果希望在备份和存档数据存储中同时使用备份副本，请选择 **立即** 复制。
    - 如果你希望在备份数据存储中将备份存储到存档数据存储，请选择 **在过期** 时将其移到 "过期"。

1. **默认保留规则**在缺少任何其他保留规则的情况下应用，默认值为3个月。

    - 保留期的范围为 **备份数据存储**中的7天到10年。
    - 保留期的范围为 **存档数据存储**中的六个月到10年。

    ![编辑保留期](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>保留规则按优先顺序计算。 **每年**规则的优先级最高，后跟**每月**和**每周**规则。 当没有其他规则合格时，将应用默认的保留设置。 例如，同一个恢复点可能是每周执行的第一次成功备份，以及每个月第一次成功备份。 不过，由于每月规则优先级高于每周规则的优先级，因此，每个月的第一次成功备份的保留期都适用。

## <a name="restore"></a>还原

如果服务在目标服务器上具有相应的权限集，则可以将数据库还原到同一订阅中的任何 Azure PostgreSQL 服务器。 请确保已配置了服务备份 Postgres 服务器所需的 [先决条件权限](#prerequisite-permissions-for-configure-backup-and-restore) 。

遵循以下分步指南来触发还原：

1. 可以通过两种方式启动还原过程：
    1. 请参阅[备份中心](backup-center-overview.md)  ->  **概述**  ->  **还原**。

    ![在备份中心选择 "还原"](./media/backup-azure-database-postgresql/backup-center-restore.png)

    在 " **启动：还原**" 下，选择 " **数据源类型** " 作为 **Azure Database for PostgreSQL**。 选择 **备份实例**。

    ![选择启动中的数据源类型：还原](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. 或者，可以直接跳到**备份保管库**  ->  **备份实例**。 选择对应于要还原的数据库的 " **备份实例** "。

    ![用于还原的备份实例](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![备份实例列表](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![选择“还原”](./media/backup-azure-database-postgresql/select-restore.png)

1. 从适用于所选备份实例的所有完整备份的列表中**选择 "恢复点**"。 默认情况下，将选择最新的恢复点。

    ![选择恢复点](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![恢复点列表](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. 输入 **还原参数**。 此时，你可以从两种类型的还原中进行选择： **还原为数据库** 并 **还原为文件**。

1. **还原为数据库**：还原备份数据以在目标 PostgreSQL 服务器上创建新数据库。

    - 目标服务器可以与源服务器相同。 但是，不支持覆盖原始数据库。
    - 你可以在所有订阅中从服务器中进行选择，但在保管库所在的同一区域中进行选择。
    - 选择 "查看" 和 " **还原**"。 这将触发验证，以检查服务在目标服务器上是否具有适当的还原权限。

    ![还原为数据库](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **还原为文件**：将备份文件转储到目标存储帐户 (blob) 。

    - 你可以从存储帐户中选择所有订阅中的存储帐户，但在保管库所在的同一区域中进行选择。
    - 从为所选存储帐户筛选的容器列表中选择目标容器。
    - 选择 "查看" 和 " **还原**"。 这将触发验证，以检查服务在目标服务器上是否具有适当的还原权限。

    ![还原为文件](./media/backup-azure-database-postgresql/restore-as-files.png)

1. 查看信息，然后选择 " **还原**"。 这会触发可以在 " **备份作业**" 下跟踪的相应还原作业。

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>配置备份和还原的先决条件权限

Azure 备份遵循严格的安全准则。 即使它是本机 Azure 服务，也不会假设对资源的权限，并且需要由用户显式提供。  同样，不会存储连接到数据库的凭据。 这对于保护数据非常重要。 相反，我们使用 Azure Active Directory 身份验证。

[下载此文档](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) 以获取自动脚本和相关说明。 它将向 Azure PostgreSQL 服务器授予一组适当的权限，以便进行备份和还原。

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>管理已备份的 Azure PostgreSQL 数据库

这些是可在 **备份实例**上执行的管理操作：

### <a name="on-demand-backup"></a>按需备份

若要触发不在策略中指定的计划的备份，请立即执行**备份实例**  ->  **备份**。
从关联的备份策略中定义的保留规则列表中进行选择。

![立即触发备份](./media/backup-azure-database-postgresql/backup-now.png)

![从保留规则列表中选择](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>停止保护

可以停止对备份项的保护。 这也会删除该备份项目的关联恢复点。 我们尚未提供停止保护的选项，同时保留现有的恢复点。

![停止保护](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>更改策略

你可以使用备份实例更改关联的策略。

1. 选择**备份实例**  ->  **更改策略**。

    ![更改策略](./media/backup-azure-database-postgresql/change-policy.png)

1. 选择要应用于数据库的新策略。

    ![重新分配策略](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>疑难解答

本部分提供有关在 Azure 备份中备份 Azure PostgreSQL 数据库的疑难解答信息。

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

在要备份或还原的 PG 服务器上授予备份保管库 MSI **读取** 访问权限：

为了建立与 PostgreSQL 数据库的安全连接，Azure 备份使用 [托管服务标识 (MSI) ](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 身份验证模型。 这意味着备份保管库将只能访问那些已被用户显式授予权限的资源。

创建时，系统 MSI 会自动分配给保管库。 需要为此保管库 MSI 授予对要从中备份数据库的 PostgreSQL 服务器的访问权限。

步骤：

1. 在 Postgres 服务器中， **访问 (IAM) ** 窗格中的 "访问控制"。

    ![访问控制窗格](./media/backup-azure-database-postgresql/access-control-pane.png)

1. 选择 " **添加角色分配**"。

    ![添加角色分配](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 在打开的右上下文窗格中，输入以下内容：<br>

    **角色：** 读取器<br>
    **将访问权限分配给：** 选择 **备份保管库**<br>
    如果在下拉列表中找不到 " **备份保管库** " 选项，请选择 **Azure AD 用户、组或服务主体选项**<br>

    ![选择角色](./media/backup-azure-database-postgresql/select-role.png)

    **选择：** 输入要将此服务器及其数据库备份到的备份保管库名称。<br>

    ![输入备份保管库名称](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

创建可使用 Azure Active Directory 进行身份验证的数据库备份用户：

此错误可能是由于 PostgreSQL 服务器缺少 Azure Active Directory 管理员，或是缺少可使用 Azure Active Directory 进行身份验证的备份用户。

步骤：

将 Active Directory 管理员添加到 OSS 服务器：

若要通过可以使用 Azure Active Directory 而不是密码进行身份验证的用户连接到数据库，则需要执行此步骤。 Azure Database for PostgreSQL 中的 Azure AD 管理员用户将拥有角色 **azure_ad_admin**。 只有 **azure_ad_admin** 角色才能创建可以使用 Azure AD 进行身份验证的新数据库用户。

1. 在服务器视图的左侧导航窗格中转到 "Active Directory 管理" 选项卡，并添加自己的 (或其他) 作为 Active Directory 管理员。

    ![设置 Active Directory 管理](./media/backup-azure-database-postgresql/set-admin.png)

1. 请确保 **保存** AD 管理员用户设置。

    ![保存 Active Directory 管理员用户设置](./media/backup-azure-database-postgresql/save-admin-setting.png)

请参阅 [本文档](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) ，了解完成权限授予步骤所需执行的步骤的列表。

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

启用服务器视图中的 " **允许访问 Azure 服务** " 标志，建立网络视觉。 在服务器视图的 " **连接安全性** " 窗格下，将 " **允许访问 Azure 服务** " 标志设置为 **"是"**。

![允许访问 Azure 服务](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>还原为文件时还原到存储帐户容器的权限

1. 为备份保管库 MSI 授予使用 Azure 门户访问存储帐户容器的权限。
    1. 请参阅**存储帐户**  ->  **访问控制**  ->  **添加角色分配**。
    1. 将 **存储 Blob 数据参与者** 角色分配到备份保管库 MSI。

    ![分配存储 Blob 数据参与者角色](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. 或者，通过使用 Azure CLI [az role create create](https://docs.microsoft.com/cli/azure/role/assignment) 命令，对要还原到的特定容器授予精细的权限。

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. 将 "代理人" 参数替换为保管库的 MSI 的 **应用程序 ID** ，并将 "作用域" 参数替换为指向特定容器。
    1. 若要获取保管库 MSI 的**应用程序 ID** ，请选择 "**应用程序类型**" 下的 "**所有应用**程序"：

        ![选择所有应用程序](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. 搜索保管库名称并复制应用程序 ID：

        ![搜索保管库名称](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>后续步骤

- [备份保管库概述](backup-vault-overview.md)
