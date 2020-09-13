---
title: Azure 内置角色 - Azure RBAC
description: 本文介绍适用于 Azure 基于角色的访问控制 (Azure RBAC) 的 Azure 内置角色。 其中列出 Actions、NotActions、DataActions 和 NotDataActions。
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 09/04/2020
ms.custom: generated
ms.openlocfilehash: 25c231265bb4ec497af5559624b7228167add76d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483450"
---
# <a name="azure-built-in-roles"></a>Azure 内置角色

[Azure 基于角色的访问控制 (Azure RBAC)](overview.md) 拥有多个 Azure 内置角色，可将其分配给用户、组、服务主体和托管标识。 角色分配是控制对 Azure 资源的访问的方式。 如果内置角色不能满足组织的具体需求，则可创建自己的 [Azure 自定义角色](custom-roles.md)。

本文列出了 Azure 内置角色，这些角色总是在不断发展。 若要获取最新角色，请使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 或 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。 如果你正在查找 Azure Active Directory (Azure AD) 的管理员角色，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

下表提供了每个内置角色的简短说明和唯一 ID。 单击角色名称，查看每个角色的 `Actions`、`NotActions`、`DataActions` 和 `NotDataActions` 列表。 有关这些操作的含义以及它们如何应用于管理和数据平面的信息，请参阅[了解 Azure 角色定义](role-definitions.md)。

## <a name="all"></a>全部

> [!div class="mx-tableFixed"]
> | 内置角色 | 说明 | ID |
> | --- | --- | --- |
> | **常规** |  |  |
> | [参与者](#contributor) | 授予管理所有资源的完全访问权限，但不允许在 Azure RBAC 中分配角色。 | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [所有者](#owner) | 授予管理所有资源的完全访问权限，包括允许在 Azure RBAC 中分配角色。 | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [读者](#reader) | 查看所有资源，但不允许进行任何更改。 | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [用户访问管理员](#user-access-administrator) | 允许管理用户对 Azure 资源的访问权限。 | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **计算** |  |  |
> | [经典虚拟机参与者](#classic-virtual-machine-contributor) | 允许管理经典虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。 | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [虚拟机管理员登录](#virtual-machine-administrator-login) | 在门户中查看虚拟机并以管理员身份登录 | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [虚拟机参与者](#virtual-machine-contributor) | 允许管理虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。 | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [虚拟机用户登录](#virtual-machine-user-login) | 在门户中查看虚拟机并以普通用户身份登录。 | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **联网** |  |  |
> | [CDN 终结点参与者](#cdn-endpoint-contributor) | 可以管理 CDN 终结点，但不能向其他用户授予访问权限。 | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN 终结点读者](#cdn-endpoint-reader) | 可以查看 CDN 终结点，但不能进行更改。 | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN 配置文件参与者](#cdn-profile-contributor) | 可以管理 CDN 配置文件及其终结点，但不能向其他用户授予访问权限。 | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN 配置文件读者](#cdn-profile-reader) | 可以查看 CDN 配置文件及其终结点，但不能进行更改。 | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [经典网络参与者](#classic-network-contributor) | 允许管理经典网络，但不允许访问这些网络。 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS 区域参与者](#dns-zone-contributor) | 允许管理 Azure DNS 中的 DNS 区域和记录集，但不允许控制对其访问的人员。 | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [网络参与者](#network-contributor) | 允许管理网络，但不允许访问这些网络。 | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [专用 DNS 区域参与者](#private-dns-zone-contributor) | 允许管理专用 DNS 区域资源，但不允许管理它们所链接到的虚拟网络。 | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [流量管理器参与者](#traffic-manager-contributor) | 允许管理流量管理器配置文件，但不允许控制谁可以访问它们。 | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **存储** |  |  |
> | [Avere 参与者](#avere-contributor) | 可以创建和管理 Avere vFXT 群集。 | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere 操作员](#avere-operator) | Avere vFXT 群集用来管理群集 | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [备份参与者](#backup-contributor) | 允许管理备份服务，但不允许创建保管库以及授予其他人访问权限 | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [备份操作员](#backup-operator) | 允许管理备份服务，但删除备份、创建保管库以及授予其他人访问权限除外 | 00c29273-979b-4161-815c-10b084fb9324 |
> | [备份读者](#backup-reader) | 可以查看备份服务，但是不能进行更改 | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [经典存储帐户参与者](#classic-storage-account-contributor) | 允许管理经典存储帐户，但不允许对其进行访问。 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [经典存储帐户密钥操作员服务角色](#classic-storage-account-key-operator-service-role) | 允许经典存储帐户密钥操作员在经典存储帐户上列出和再生成密钥 | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box 参与者](#data-box-contributor) | 可让你管理 Data Box 服务下的所有内容，但不能向其他人授予访问权限。 | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box 读者](#data-box-reader) | 可让你管理 Data Box 服务，但不能创建订单或编辑订单详细信息，以及向其他人授予访问权限。 | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics 开发人员](#data-lake-analytics-developer) | 允许提交、监视和管理自己的作业，但是不允许创建或删除 Data Lake Analytics 帐户。 | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [读取器和数据访问](#reader-and-data-access) | 允许查看所有内容，但不允许删除或创建存储帐户或包含的资源。 它还允许使用存储帐户密钥对存储帐户中包含的所有数据进行读/写访问。 | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [存储帐户参与者](#storage-account-contributor) | 允许管理存储帐户。 提供对帐户密钥的访问权限，它可用于通过共享密钥授权访问数据。 | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [存储帐户密钥操作员服务角色](#storage-account-key-operator-service-role) | 允许列出和重新生成存储帐户访问密钥。 | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [存储 Blob 数据参与者](#storage-blob-data-contributor) | 读取、写入和删除 Azure 存储容器和 Blob。 若要了解给定数据操作所需的操作，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [存储 Blob 数据所有者](#storage-blob-data-owner) | 提供对 Azure 存储 Blob 容器和数据的完全访问权限，包括分配 POSIX 访问控制。 若要了解给定数据操作所需的操作，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [存储 Blob 数据读者](#storage-blob-data-reader) | 读取和列出 Azure 存储容器和 Blob。 若要了解给定数据操作所需的操作，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [存储 Blob 委托者](#storage-blob-delegator) | 获取用户委托密钥，该密钥随后可用于为使用 Azure AD 凭据签名的容器或 Blob 创建共享访问签名。 有关详细信息，请参阅[创建用户委托 SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)。 | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [存储文件数据 SMB 共享参与者](#storage-file-data-smb-share-contributor) | 允许对 Azure 文件共享中的文件/目录进行读取、写入和删除访问。 此角色在 Windows 文件服务器上没有内置的等效角色。 | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [存储文件数据 SMB 共享提升参与者](#storage-file-data-smb-share-elevated-contributor) | 允许读取、写入、删除和修改 Azure 文件共享中文件/目录上的 ACL。 此角色等效于 Windows 文件服务器上的文件共享更改 ACL。 | a7264617-510b-434b-a828-9731dc254ea7 |
> | [存储文件数据 SMB 共享读取者](#storage-file-data-smb-share-reader) | 允许对 Azure 文件共享中的文件/目录进行读取访问。 此角色等效于 Windows 文件服务器上的文件共享读取 ACL。 | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [存储队列数据参与者](#storage-queue-data-contributor) | 读取、写入和删除 Azure 存储队列和队列消息。 若要了解给定数据操作所需的操作，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [存储队列数据消息处理器](#storage-queue-data-message-processor) | 在 Azure 存储队列中扫视、检索和删除消息。 若要了解给定数据操作所需的操作，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [存储队列数据消息发送方](#storage-queue-data-message-sender) | 将消息添加到 Azure 存储队列。 若要了解给定数据操作所需的操作，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [存储队列数据读取者](#storage-queue-data-reader) | 读取并列出 Azure 存储队列和队列消息。 若要了解给定数据操作所需的操作，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps 数据读取器](#azure-maps-data-reader) | 授予从 Azure Maps 帐户中读取地图相关数据的权限。 | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [搜索服务参与者](#search-service-contributor) | 允许管理搜索服务，但不允许访问这些服务。 | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Web 计划参与者](#web-plan-contributor) | 允许管理网站的 Web 计划，但不允许访问这些计划。 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [网站参与者](#website-contributor) | 允许管理网站（而非 Web 计划），但不允许访问这些网站。 | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **容器** |  |  |
> | [AcrDelete](#acrdelete) | acr 删除 | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR 映像签名程序 | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr 拉取 | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr 推送 | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR 隔离数据读取器 | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR 隔离数据编写器 | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes 服务群集管理员角色](#azure-kubernetes-service-cluster-admin-role) | 列出群集管理员凭据操作。 | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes 服务群集用户角色](#azure-kubernetes-service-cluster-user-role) | 列出群集用户凭据操作。 | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes 服务参与者角色](#azure-kubernetes-service-contributor-role) | 授予对 Azure Kubernetes 服务群集的读写访问权限 | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes 服务 RBAC 管理员](#azure-kubernetes-service-rbac-admin) | 允许管理群集/命名空间下的所有资源，但不能更新或删除资源配额和命名空间。 | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes 服务 RBAC 群集管理员](#azure-kubernetes-service-rbac-cluster-admin) | 允许管理群集中的所有资源。 | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes 服务 RBAC 读取者](#azure-kubernetes-service-rbac-reader) | 允许查看群集/命名空间中除密码之外的所有资源。 | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes 服务 RBAC 写入者](#azure-kubernetes-service-rbac-writer) | 允许更新群集/命名空间中的所有内容，但资源配额、命名空间、pod 安全策略、证书签名请求、(群集)角色和(群集)角色绑定除外。 | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **数据库** |  |  |
> | [Cosmos DB 帐户读者角色](#cosmos-db-account-reader-role) | 可以读取 Azure Cosmos DB 帐户数据。 请参阅 [Cosmos DB 帐户参与者](#documentdb-account-contributor)，了解如何管理 Azure Cosmos DB 帐户。 | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB 操作员](#cosmos-db-operator) | 允许管理 Azure Cosmos DB 帐户，但不能访问其中的数据。 阻止访问帐户密钥和连接字符串。 | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | 可以为帐户提交 Cosmos DB 数据库或容器的还原请求 | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [DocumentDB 帐户参与者](#documentdb-account-contributor) | 可管理 Azure Cosmos DB 帐户。 Azure Cosmos DB 以前称为 DocumentDB。 | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis 缓存参与者](#redis-cache-contributor) | 允许管理 Redis 缓存，但不允许访问这些缓存。 | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB 参与者](#sql-db-contributor) | 允许管理 SQL 数据库，但不允许访问这些数据库。 此外，不允许管理其安全相关的策略或其父 SQL 服务器。 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL 托管实例参与者](#sql-managed-instance-contributor) | 允许你管理 SQL 托管实例和必需的网络配置，但无法向其他人授予访问权限。 | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL 安全管理器](#sql-security-manager) | 允许管理 SQL 服务器和数据库的安全相关策略，但不允许访问它们。 | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server 参与者](#sql-server-contributor) | 允许管理 SQL Server 和数据库，但不允许访问它们及其安全相关策略。 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **分析** |  |  |
> | [Azure 事件中心数据所有者](#azure-event-hubs-data-owner) | 允许完全访问 Azure 事件中心资源。 | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure 事件中心数据接收方](#azure-event-hubs-data-receiver) | 允许接收对 Azure 事件中心资源的访问权限。 | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure 事件中心数据发送方](#azure-event-hubs-data-sender) | 允许以发送方式访问 Azure 事件中心资源。 | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [数据工厂参与者](#data-factory-contributor) | 创建和管理数据工厂，以及其中的子资源。 | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [数据清除程序](#data-purger) | 可清除分析数据 | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight 群集操作员](#hdinsight-cluster-operator) | 允许你读取和修改 HDInsight 群集配置。 | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight 域服务参与者](#hdinsight-domain-services-contributor) | 可以读取、创建、修改和删除 HDInsight 企业安全性套餐所需的域服务相关操作 | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics 参与者](#log-analytics-contributor) | Log Analytics 参与者可以读取所有监视数据并编辑监视设置。 编辑监视设置包括向 VM 添加 VM 扩展、读取存储帐户密钥以便能够从 Azure 存储配置日志收集、创建和配置自动化帐户、添加解决方案以及配置所有 Azure 资源上的 Azure 诊断。 | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics 读者](#log-analytics-reader) | Log Analytics 读者可以查看和搜索所有监视数据并查看监视设置，其中包括查看所有 Azure 资源上的 Azure 诊断的配置。 | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **区块链** |  |  |
> | [区块链成员节点访问（预览版）](#blockchain-member-node-access-preview) | 允许对区块链成员节点的访问 | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | AI + 机器学习 |  |  |
> | [认知服务参与者](#cognitive-services-contributor) | 允许创建、读取、更新、删除和管理认知服务的密钥。 | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [认知服务数据读取者（预览版）](#cognitive-services-data-reader-preview) | 允许读取认知服务数据。 | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [认知服务用户](#cognitive-services-user) | 允许读取和列出认知服务的密钥。 | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | 混合现实 |  |  |
> | [远程渲染管理员](#remote-rendering-administrator) | 为用户提供 Azure 远程渲染的转换、管理会话、渲染和诊断功能 | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [远程渲染客户端](#remote-rendering-client) | 为用户提供 Azure 远程渲染的管理会话、渲染和诊断功能。 | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [空间定位点帐户参与者](#spatial-anchors-account-contributor) | 允许管理帐户中的空间定位点，但不能删除它们 | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [空间定位点帐户所有者](#spatial-anchors-account-owner) | 允许管理帐户中的空间定位点，包括删除它们 | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [空间定位点帐户读取者](#spatial-anchors-account-reader) | 允许查找并读取帐户中的空间定位点的属性 | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **集成** |  |  |
> | [API 管理服务参与者](#api-management-service-contributor) | 可以管理服务和 API | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API 管理服务操作员角色](#api-management-service-operator-role) | 可以管理服务，但不可管理 API | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API 管理服务读者角色](#api-management-service-reader-role) | 对服务和 API 的只读访问权限 | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [应用程序配置数据所有者](#app-configuration-data-owner) | 允许对应用程序配置数据进行完全访问。 | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [应用程序配置数据读取者](#app-configuration-data-reader) | 允许对应用程序配置数据进行读取访问。 | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure 服务总线数据所有者](#azure-service-bus-data-owner) | 允许完全访问 Azure 服务总线资源。 | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure 服务总线数据接收方](#azure-service-bus-data-receiver) | 允许对 Azure 服务总线资源进行接收访问。 | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure 服务总线数据发送方](#azure-service-bus-data-sender) | 允许对 Azure 服务总线资源进行发送访问。 | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack 注册所有者](#azure-stack-registration-owner) | 允许管理 Azure Stack 注册。 | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription 参与者](#eventgrid-eventsubscription-contributor) | 可以管理 EventGrid 事件订阅操作。 | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription 读者](#eventgrid-eventsubscription-reader) | 可以读取 EventGrid 事件订阅。 | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR 数据参与者](#fhir-data-contributor) | 角色允许用户或主体完全访问 FHIR 数据 | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR 数据导出者](#fhir-data-exporter) | 角色允许用户或主体读取和导出 FHIR 数据 | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR 数据读取者](#fhir-data-reader) | 角色允许用户或主体读取 FHIR 数据 | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR 数据写入者](#fhir-data-writer) | 角色允许用户或主体读取和写入 FHIR 数据 | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [集成服务环境参与者](#integration-service-environment-contributor) | 允许管理集成服务环境，但不允许访问这些环境。 | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [集成服务环境开发人员](#integration-service-environment-developer) | 允许开发人员在集成服务环境中创建和更新工作流、集成帐户与 API 连接。 | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Intelligent Systems 帐户参与者](#intelligent-systems-account-contributor) | 允许管理智能系统帐户，但不允许访问这些帐户。 | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [逻辑应用参与者](#logic-app-contributor) | 允许管理逻辑应用，但不允许更改其访问权限。 | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [逻辑应用操作员](#logic-app-operator) | 允许读取、启用和禁用逻辑应用，但不允许编辑或更新它们。 | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **标识** |  |  |
> | [托管的标识参与者](#managed-identity-contributor) | 创建、读取、更新和删除用户分配的标识 | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [托管的标识操作员](#managed-identity-operator) | 读取和分配用户分配的标识 | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **安全性** |  |  |
> | [Azure Sentinel 参与者](#azure-sentinel-contributor) | Azure Sentinel 参与者 | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel 读取者](#azure-sentinel-reader) | Azure Sentinel 读取者 | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel 响应方](#azure-sentinel-responder) | Azure Sentinel 响应方 | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault 管理员 (预览版) ](#key-vault-administrator-preview) | 对密钥保管库以及其中的所有对象（包括证书、密钥和机密）执行所有数据平面操作。 无法管理 key vault 资源或管理角色分配。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [ (预览版 Key Vault 证书官员) ](#key-vault-certificates-officer-preview) | 对密钥保管库的证书执行任何操作（管理权限除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [密钥保管库参与者](#key-vault-contributor) | 管理密钥保管库，但不允许在 Azure RBAC 中分配角色，而且不允许你访问机密、密钥或证书。 | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [ (预览版 Key Vault 加密官) ](#key-vault-crypto-officer-preview) | 对密钥保管库的密钥执行任何操作（"管理权限" 除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault 加密服务加密 (预览版) ](#key-vault-crypto-service-encryption-preview) | 读取密钥的元数据并执行包装/解包操作。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault 加密用户 (预览版) ](#key-vault-crypto-user-preview) | 使用密钥执行加密操作。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault Reader (预览版) ](#key-vault-reader-preview) | 读取密钥保管库及其证书、密钥和机密的元数据。 无法读取机密内容或密钥材料之类的敏感值。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [ (预览版 Key Vault 秘密专员) ](#key-vault-secrets-officer-preview) | 对密钥保管库的密码执行任何操作（"管理权限" 除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault 机密用户 (预览版) ](#key-vault-secrets-user-preview) | 读取机密内容。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [安全管理员](#security-admin) | 查看和更新安全中心的权限。 与安全读取者角色具有相同的权限，还可以更新安全策略并关闭警报和建议。 | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [安全评估参与者](#security-assessment-contributor) | 允许你将评估推送到安全中心 | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [安全管理器（旧版）](#security-manager-legacy) | 这是旧角色。 请改用安全管理员。 | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [安全读取者](#security-reader) | 查看安全中心的权限。 可以查看但不能更改建议、警报、安全策略和安全状态。 | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest 实验室用户](#devtest-labs-user) | 允许连接、启动、重启和关闭 Azure 开发测试实验室中的虚拟机。 | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [实验室创建者](#lab-creator) | 允许在 Azure 实验室帐户下新建实验室。 | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **监视** |  |  |
> | [Application Insights 组件参与者](#application-insights-component-contributor) | 可管理 Application Insights 组件 | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights 快照调试器](#application-insights-snapshot-debugger) | 授予用户查看和下载使用 Application Insights Snapshot Debugger 收集的调试快照的权限。 请注意，[所有者](#owner)或[参与者](#contributor)角色不包括这些权限。 在向用户授予 Application Insights Snapshot Debugger 角色时，必须将该角色直接授予用户。 将角色添加到自定义角色时，无法识别该角色。 | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [监视参与者](#monitoring-contributor) | 可以读取所有监视数据和编辑监视设置。 另请参阅 [Azure Monitor 的角色、权限和安全入门](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)。 | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [监视指标发布者](#monitoring-metrics-publisher) | 允许针对 Azure 资源发布指标 | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [监视读取者](#monitoring-reader) | 可以读取所有监视数据（指标、日志等）。 另请参阅 [Azure Monitor 的角色、权限和安全入门](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)。 | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [工作簿参与者](#workbook-contributor) | 可以保存共享的工作簿。 | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [工作簿读者](#workbook-reader) | 可以读取工作簿。 | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | 管理 + 治理 |  |  |
> | [自动化作业操作员](#automation-job-operator) | 使用自动化 Runbook 创建和管理作业。 | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [自动化运算符](#automation-operator) | 自动化操作员能够启动、停止、暂停和恢复作业 | d3881f73-407a-4167-8283-e981cbba0404 |
> | [自动化 Runbook 操作员](#automation-runbook-operator) | 读取 Runbook 属性 - 以能够创建 runbook 的作业。 | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Connected Machine 加入](#azure-connected-machine-onboarding) | 可以加入 Azure Connected Machine。 | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine 资源管理员](#azure-connected-machine-resource-administrator) | 可以读取、写入、删除和重新加入 Azure Connected Machine。 | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [计费读者](#billing-reader) | 允许对帐单数据进行读取访问 | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [蓝图参与者](#blueprint-contributor) | 可以管理蓝图定义，但不能对其进行分配。 | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [蓝图操作员](#blueprint-operator) | 可以指定现有已发布的蓝图，但不能创建新的蓝图。 请注意：仅当使用用户分配的托管标识完成分配时，此分配才有效。 | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [成本管理参与者](#cost-management-contributor) | 可以查看成本和管理成本配置（例如预算、导出） | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [成本管理读者](#cost-management-reader) | 可以查看成本数据和配置（例如预算、导出） | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [层次结构设置管理员](#hierarchy-settings-administrator) | 允许用户编辑和删除层次结构设置 | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes 群集 - Azure Arc 载入](#kubernetes-cluster---azure-arc-onboarding) | 授权任何用户/服务创建 connectedClusters 资源的角色定义 | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [托管应用程序参与者角色](#managed-application-contributor-role) | 允许创建托管应用程序资源。 | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [托管应用程序操作员角色](#managed-application-operator-role) | 可让你在托管应用程序资源上读取和执行操作 | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [托管应用程序读者](#managed-applications-reader) | 允许读取托管应用中的资源并请求 JIT 访问。 | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [托管服务注册分配删除角色](#managed-services-registration-assignment-delete-role) | 托管服务注册分配删除角色允许管理租户用户删除分配给其租户的注册分配。 | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [管理组参与者](#management-group-contributor) | 管理组参与者角色 | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [管理组读取者](#management-group-reader) | 管理组读取者角色 | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New elic APM 帐户参与者](#new-relic-apm-account-contributor) | 允许管理 New Relic 应用程序性能管理帐户和应用程序，但不允许访问它们。 | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [策略见解数据编写者（预览）](#policy-insights-data-writer-preview) | 允许对资源策略进行读取访问，并允许对资源组件策略事件进行写入访问。 | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [资源策略参与者](#resource-policy-contributor) | 有权创建/修改资源策略、创建支持票证和读取资源/层次结构的用户。 | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery 参与者](#site-recovery-contributor) | 允许管理除保管库创建和角色分配外的 Site Recovery 服务 | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery 操作员](#site-recovery-operator) | 允许进行故障转移和故障回复，但不允许执行其他 Site Recovery 管理操作 | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery 读取者](#site-recovery-reader) | 允许查看 Site Recovery 状态，但不允许执行其他管理操作 | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [支持请求参与者](#support-request-contributor) | 允许创建和管理支持请求 | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [标记参与者](#tag-contributor) | 允许你管理实体上的标记，而无需提供对实体本身的访问权限。 | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **其他** |  |  |
> | [BizTalk 参与者](#biztalk-contributor) | 允许管理 BizTalk 服务，但不允许访问这些服务。 | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [桌面虚拟化用户](#desktop-virtualization-user) | 允许用户使用应用程序组中的应用程序。 | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [计划程序作业集合参与者](#scheduler-job-collections-contributor) | 允许管理计划程序作业集合，但不允许访问这些集合。 | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>常规


### <a name="contributor"></a>参与者

授予管理所有资源的完全访问权限，但不允许在 Azure RBAC 中分配角色。 [了解详细信息](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | * | 创建和管理所有类型的资源 |
> | **不操作** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | 删除角色、策略分配、策略定义和策略集定义 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | 创建角色、角色分配、策略分配、策略定义和策略集定义 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | 向调用方授予租户范围的“用户访问管理员”访问权限 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | 创建或更新任何蓝图分配 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | 删除任何蓝图分配 |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>所有者

授予管理所有资源的完全访问权限，包括允许在 Azure RBAC 中分配角色。 [了解详细信息](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | * | 创建和管理所有类型的资源 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>读取器

查看所有资源，但不允许进行任何更改。 [了解详细信息](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>用户访问管理员

允许管理用户对 Azure 资源的访问权限。 [了解详细信息](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | 管理授权 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>计算


### <a name="classic-virtual-machine-contributor"></a>经典虚拟机参与者

允许管理经典虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | 创建和管理经典计算域名 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | 创建和管理虚拟机 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | 链接保留 IP |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | 获取保留 IP |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | 加入虚拟网络。 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | 获取虚拟网络。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | 返回存储帐户磁盘。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | 返回存储帐户映像。 （已弃用。 请使用“Microsoft.ClassicStorage/storageAccounts/vmImages”） |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 返回包含给定帐户的存储帐户。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>虚拟机管理员登录

查看门户中的虚拟机，并以管理员身份登录 [了解详细信息](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 获取负载均衡器定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 获取网络接口定义。  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 以普通用户身份登录虚拟机 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | 以 Windows 管理员身份或 Linux 根用户权限登录虚拟机 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>虚拟机参与者

允许管理虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。 [了解详细信息](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | 创建和管理计算可用性集 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | 创建和管理计算位置 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | 执行所有虚拟机操作，包括创建、更新、删除、启动、重新启动和关闭虚拟机。 在虚拟机上执行预定义的脚本。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | 创建和管理虚拟机规模集 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | 创建新的磁盘，或更新现有的磁盘 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | 获取磁盘的属性 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | 删除磁盘 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | 加入应用程序网关后端地址池。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 加入负载均衡器后端地址池。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | 加入负载均衡器入站 NAT 池。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 加入负载均衡器入站 NAT 规则。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | 允许使用负载均衡器的探测。 例如，使用此权限，VM 规模集的 healthProbe 属性可以引用探测。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 获取负载均衡器定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | 创建和管理网络位置 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | 创建和管理网络接口 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 加入网络安全组。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | 获取网络安全组定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 加入公共 IP 地址。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虚拟网络。 不可发出警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 创建备份保护意向 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 创建备份受保护项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 返回所有保护策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | 创建保护策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | “创建保管库”操作创建“vault”类型的 Azure 资源 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>虚拟机用户登录

在门户中查看虚拟机并以普通用户身份登录。 [了解详细信息](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 获取负载均衡器定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 获取网络接口定义。  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 以普通用户身份登录虚拟机 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>网络


### <a name="cdn-endpoint-contributor"></a>CDN 终结点参与者

可以管理 CDN 终结点，但不能向其他用户授予访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN 终结点读者

可以查看 CDN 终结点，但不能进行更改。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN 配置文件参与者

可以管理 CDN 配置文件及其终结点，但不能向其他用户授予访问权限。 [了解详细信息](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN 配置文件读者

可以查看 CDN 配置文件及其终结点，但不能进行更改。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>经典网络参与者

允许管理经典网络，但不允许访问这些网络。 [了解详细信息](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | 创建和管理经典网络 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS 区域参与者

允许管理 Azure DNS 中的 DNS 区域和记录集，但不允许控制对其访问的人员。 [了解详细信息](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | 创建和管理 DNS 区域和记录 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>网络参与者

允许管理网络，但不允许访问这些网络。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | 创建并管理网络 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>专用 DNS 区域参与者

允许管理专用 DNS 区域资源，但不允许管理它们所链接到的虚拟网络。 [了解详细信息](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | 加入虚拟网络。 不可发出警报。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>流量管理器参与者

允许管理流量管理器配置文件，但不允许控制谁可以访问它们。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>存储


### <a name="avere-contributor"></a>Avere 参与者

可以创建和管理 Avere vFXT 群集。 [了解详细信息](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 获取虚拟网络子网定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虚拟网络。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 加入网络安全组。 不可发出警报。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 创建和管理存储帐户 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | 获取资源组的资源。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | 返回删除 blob 的结果 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 返回 blob 或 blob 列表 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | 返回写入 blob 的结果 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere 操作员

由 Avere vFXT 群集用来管理群集 [了解详细信息](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 获取虚拟机的属性 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 获取网络接口定义。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 创建网络接口，或更新现有的网络接口。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 获取虚拟网络子网定义 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虚拟网络。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 加入网络安全组。 不可发出警报。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 返回删除容器的结果 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 返回容器列表 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | 返回放置 blob 容器的结果 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | 返回删除 blob 的结果 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 返回 blob 或 blob 列表 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | 返回写入 blob 的结果 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>备份参与者

允许管理备份服务，但不允许创建保管库及授予他人访问权限 [了解详细信息](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | 管理备份管理操作的结果 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | 在恢复服务保管库的备份结构内创建和管理备份容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 刷新容器列表 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 创建和管理备份作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 导出作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 创建和管理备份管理操作的结果 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | 创建和管理备份策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 创建和管理可以备份的项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | 创建和管理备份项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | 创建和管理保存备份项的容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | 创建和管理与恢复服务保管库中的备份相关的证书 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 创建和管理与保管库相关的扩展信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 创建和管理已注册标识 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | 创建和管理恢复服务保管库的使用情况 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 验证对受保护项的操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | “创建保管库”操作创建“vault”类型的 Azure 资源 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | 返回恢复服务保管库的备份操作状态。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 返回使用保管库注册的所有备份管理服务器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 获取所有可保护的容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | 检查恢复服务保管库的备份状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 验证功能 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 解决警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 操作返回资源提供程序的操作列表 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 获取给定操作的操作状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 列出所有备份保护意向 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>备份操作员

允许管理备份服务，但删除备份、创建保管库及授予他人访问权限除外 [了解详细信息](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 返回操作状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 获取对保护容器执行的操作的结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 对受保护的项执行备份。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 获取对受保护项执行的操作的结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 返回对受保护项执行的操作的状态。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 预配受保护项的即时项恢复 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 获取受保护项的恢复点。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 还原受保护项的恢复点。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 吊销受保护项的即时项恢复 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 创建备份受保护项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 返回所有已注册的容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 刷新容器列表 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 创建和管理备份作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 导出作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 创建和管理备份管理操作的结果 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 获取策略操作的结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 返回所有保护策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 创建和管理可以备份的项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 返回所有受保护项的列表。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 返回属于订阅的所有容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | “注册服务容器”操作可用于向恢复服务注册容器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 验证对受保护项的操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | 返回恢复服务保管库的备份操作状态。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 获取策略操作的状态。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | 创建已注册的容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | 在容器内进行工作负载的查询 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 返回使用保管库注册的所有备份管理服务器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 创建备份保护意向 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 获取备份保护意向 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 获取所有可保护的容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 获取容器中的所有项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | 检查恢复服务保管库的备份状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 验证功能 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 解决警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 操作返回资源提供程序的操作列表 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 获取给定操作的操作状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 列出所有备份保护意向 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>备份读取器

可以查看备份服务，但不能进行更改 [了解详细信息](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 返回操作状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 获取对保护容器执行的操作的结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 获取对受保护项执行的操作的结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 返回对受保护项执行的操作的状态。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 获取受保护项的恢复点。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 返回所有已注册的容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | 返回作业操作的结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | 返回所有作业对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 导出作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | 返回恢复服务保管库的备份操作结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 获取策略操作的结果。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 返回所有保护策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 返回所有受保护项的列表。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 返回属于订阅的所有容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | 返回恢复服务保管库的存储配置。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | 返回恢复服务保管库的配置。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | 返回恢复服务保管库的备份操作状态。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 获取策略操作的状态。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 返回使用保管库注册的所有备份管理服务器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 获取备份保护意向 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 获取容器中的所有项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | 检查恢复服务保管库的备份状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 解决警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 操作返回资源提供程序的操作列表 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 获取给定操作的操作状态 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 列出所有备份保护意向 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 验证功能 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>经典存储帐户参与者

允许管理经典存储帐户，但不允许对其进行访问。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | 创建和管理存储帐户 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>经典存储帐户密钥操作员服务角色

允许经典存储帐户密钥操作员在经典存储帐户上列出和再生成密钥 [了解详细信息](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | 列出存储帐户的访问密钥。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | 再生成存储帐户的现有访问密钥。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box 参与者

可让你管理 Data Box 服务下的所有内容，但不能向其他人授予访问权限。 [了解详细信息](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box 读者

可让你管理 Data Box 服务，但不能创建订单或编辑订单详细信息，以及向其他人授予访问权限。 [了解详细信息](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | 列出与订单相关的未加密凭据。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | 此方法返回可用 SKU 列表。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | 此方法执行所有类型的验证。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | 此方法返回区域的配置。 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | 验证送货地址，并提供备用地址（如有）。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics 开发人员

允许提交、监视和管理自己的作业，但是不允许创建或删除 Data Lake Analytics 帐户。 [了解详细信息](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | 删除 DataLakeAnalytics 帐户。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | 授予取消由其他用户提交的作业的权限。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | 创建或更新 DataLakeAnalytics 帐户。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | 获取或更新 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | 从 DataLakeAnalytics 帐户取消链接 DataLakeStore 帐户。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | 创建或更新 DataLakeAnalytics 帐户的链接存储帐户。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | 从 DataLakeAnalytics 帐户取消链接存储帐户。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | 创建或更新防火墙规则。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | 删除防火墙规则。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | 创建或更新计算策略。 |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | 删除计算策略。 |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>读取器和数据访问

允许查看所有内容，但不允许删除或创建存储帐户或包含的资源。 它还允许使用存储帐户密钥对存储帐户中包含的所有数据进行读/写访问。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | 返回指定存储帐户的帐户 SAS 令牌。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>存储帐户参与者

允许管理存储帐户。 提供对帐户密钥的访问权限，而帐户密钥可以用来通过共享密钥授权对数据进行访问。 [了解详细信息](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 创建、更新或读取 Analysis Server 的诊断设置 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 不可发出警报。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 创建和管理存储帐户 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>存储帐户密钥操作员服务角色

允许列出和重新生成存储帐户访问密钥。 [了解详细信息](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 返回指定存储帐户的访问密钥。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | 再生成指定存储帐户的访问密钥。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>存储 Blob 数据参与者

读取、写入和删除 Azure 存储容器和 Blob。 若要了解需要对给定的数据执行哪些操作，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [了解详细信息](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 删除容器。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 返回容器或容器列表。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | 修改容器的元数据或属性。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 返回 Blob 服务的用户委托密钥。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | 删除 Blob。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 返回 Blob 或 Blob 列表。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | 将 Blob 从一个路径移到另一个路径 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | 写入到 Blob。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>存储 Blob 数据所有者

提供对 Azure 存储 Blob 容器和数据的完全访问权限，包括分配 POSIX 访问控制。 若要了解需要对给定的数据执行哪些操作，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [了解详细信息](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | 对容器的完全权限。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 返回 Blob 服务的用户委托密钥。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | 对 Blob 的完全权限。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>存储 Blob 数据读取者

读取和列出 Azure 存储容器和 Blob。 若要了解需要对给定的数据执行哪些操作，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [了解详细信息](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 返回容器或容器列表。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 返回 Blob 服务的用户委托密钥。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 返回 Blob 或 Blob 列表。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>存储 Blob 委托者

获取用户委托密钥，该密钥随后可用于为使用 Azure AD 凭据签名的容器或 Blob 创建共享访问签名。 有关详细信息，请参阅[创建用户委托 SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)。 [了解详细信息](https://docs.microsoft.com/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 返回 Blob 服务的用户委托密钥。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>存储文件数据 SMB 共享参与者

允许针对 Azure 文件共享中的文件/目录的读取、写入和删除权限。 在 Windows 文件服务器上，此角色没有内置的等效角色。 [了解详细信息](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 返回某个文件/文件夹，或文件/文件夹列表。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 返回写入文件或创建文件夹的结果。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 返回删除文件/文件夹的结果。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>存储文件数据 SMB 共享提升参与者

允许读取、写入、删除和修改 Azure 文件共享中文件/目录上的 ACL。 此角色等效于 Windows 文件服务器上更改的文件共享 ACL。 [了解详细信息](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 返回某个文件/文件夹，或文件/文件夹列表。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 返回写入文件或创建文件夹的结果。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 返回删除文件/文件夹的结果。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | 返回修改文件/文件夹权限的结果。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>存储文件数据 SMB 共享读取者

允许针对 Azure 文件共享中的文件/目录的读取权限。 此角色等效于 Windows 文件服务器上读取的文件共享 ACL。 [了解详细信息](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 返回某个文件/文件夹，或文件/文件夹列表。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>存储队列数据参与者

读取、写入和删除 Azure 存储队列和队列消息。 若要了解需要对给定的数据执行哪些操作，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [了解详细信息](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | 删除队列。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 返回队列或队列列表。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | 修改队列元数据或属性。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | 从队列中删除一个或多个消息。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 扫视或检索队列中的一个或多个消息。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | 向队列添加消息。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>存储队列数据消息处理器

速览、检索和删除 Azure 存储队列中的消息。 若要了解需要对给定的数据执行哪些操作，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [了解详细信息](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 扫视消息。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | 检索和删除消息。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>存储队列数据消息发送者

将消息添加到 Azure 存储队列。 若要了解需要对给定的数据执行哪些操作，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [了解详细信息](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | 向队列添加消息。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>存储队列数据读取者

读取并列出 Azure 存储队列和队列消息。 若要了解需要对给定的数据执行哪些操作，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [了解详细信息](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 返回队列或队列列表。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 扫视或检索队列中的一个或多个消息。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader"></a>Azure Maps 数据读取器

授予从 Azure Maps 帐户中读取地图相关数据的权限。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>搜索服务参与者

允许管理搜索服务，但不允许访问这些服务。 [了解详细信息](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | 创建和管理搜索服务 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Web 计划参与者

允许管理网站的 Web 计划，但不允许访问这些计划。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | 创建和管理服务器场 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | 加入应用服务环境 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>网站参与者

允许管理网站（而非 Web 计划），但不允许访问这些网站。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | 创建和管理 Insights 组件 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | 创建和管理网站证书 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | 获取分配给主机名的站点名称。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | 加入应用服务计划 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | 获取应用服务计划的属性 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | 创建和管理网站（站点创建还需要对关联的应用服务计划有写入权限） |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>容器


### <a name="acrdelete"></a>AcrDelete

acr 删除 [了解详细信息](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | 删除容器注册表中的项目。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

acr 映像签名程序 [了解详细信息](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | 推送/拉取容器注册表的内容信任元数据。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr 拉取 [了解详细信息](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 从容器注册表中拉取或获取映像。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr 推送 [了解详细信息](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 从容器注册表中拉取或获取映像。 |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | 将映像推送或写入容器注册表。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR 隔离数据读取器

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 从容器注册表中拉取或获取已隔离的映像 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR 隔离数据编写器

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 从容器注册表中拉取或获取已隔离的映像 |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | 写入/修改已隔离映像的隔离状态 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes 服务群集管理员角色

列出群集管理员凭据操作。 [了解详细信息](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | 列出托管群集的 clusterAdmin 凭据 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | 使用列表凭据按角色名称获取托管的群集访问配置文件 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 获取托管的群集 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes 服务群集用户角色

列出群集用户凭据操作。 [了解详细信息](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出托管群集的 clusterUser 凭据 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 获取托管的群集 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes 服务参与者角色

授予对 Azure Kubernetes 服务群集的读写访问权限[了解更多](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 获取托管的群集 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | 创建新的或更新现有的托管的群集 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes 服务 RBAC 管理员

允许管理群集/命名空间下的所有资源，但不能更新或删除资源配额和命名空间。 [了解详细信息](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 创建或更新部署。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出托管群集的 clusterUser 凭据 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | 写入 resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | 删除 resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | 写入 namespaces |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | 删除 namespaces |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes 服务 RBAC 群集管理员

允许管理群集中的所有资源。 [了解详细信息](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 创建或更新部署。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出托管群集的 clusterUser 凭据 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes 服务 RBAC 读取者

允许查看群集/命名空间中除密码之外的所有资源。 [了解详细信息](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 创建或更新部署。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出托管群集的 clusterUser 凭据 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/read |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/read |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/write |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/secrets/*"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes 服务 RBAC 写入者

允许更新群集/命名空间中的所有内容，但资源配额、命名空间、pod 安全策略、证书签名请求、(群集)角色和(群集)角色绑定除外。 [了解详细信息](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 创建或更新部署。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出托管群集的 clusterUser 凭据 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/read |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/*/write |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/read |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/rbac.authorization.k8s.io/*/write |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | 写入 namespaces |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | 写入 resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/certificates.k8s.io/certificatesigningrequests/write | 写入 certificatesigningrequests |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/podsecuritypolicies/write | 写入 podsecuritypolicies |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except resource quotas, namespaces, pod security policies, certificate signing requests, (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*/read",
        "Microsoft.ContainerService/managedClusters/*/write"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/read",
        "Microsoft.ContainerService/managedClusters/rbac.authorization.k8s.io/*/write",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/certificates.k8s.io/certificatesigningrequests/write",
        "Microsoft.ContainerService/managedClusters/policy/podsecuritypolicies/write"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>数据库


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB 帐户读者角色

可以读取 Azure Cosmos DB 帐户数据。 请参阅 [Cosmos DB 帐户参与者](#documentdb-account-contributor)，了解如何管理 Azure Cosmos DB 帐户。 [了解详细信息](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | 读取任何集合 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | 读取数据库帐户只读密钥。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | 读取指标定义 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | 添加指标 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB 操作员

允许管理 Azure Cosmos DB 帐户，但不能访问其中的数据。 阻止访问帐户密钥和连接字符串。 [了解详细信息](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 不可发出警报。 |
> | **不操作** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

可以为帐户提交 Cosmos DB 数据库或容器的还原请求 [了解详细信息](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | 提交配置备份的请求 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | 提交还原请求 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB 帐户参与者

可管理 Azure Cosmos DB 帐户。 Azure Cosmos DB 以前称为 DocumentDB。 [了解详细信息](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | 创建并管理 Azure Cosmos DB 帐户 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 不可发出警报。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis 缓存参与者

允许管理 Redis 缓存，但不允许访问这些缓存。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | 将“Microsoft.Cache”资源提供程序注册到订阅 |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | 创建和管理 Redis 缓存 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB 参与者

允许管理 SQL 数据库，但不允许访问这些数据库。 此外，不允许管理其安全相关的策略或其父 SQL 服务器。 [了解详细信息](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | 创建和管理 SQL 数据库 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 添加指标 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 读取指标定义 |
> | **不操作** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingPolicies/* | 编辑审核策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | 编辑审核设置 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 检索数据库 Blob 审核记录 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/connectionPolicies/* | 编辑连接策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | 编辑数据屏蔽策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | 编辑安全警报策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | 编辑安全度量值 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL 托管实例参与者

允许你管理 SQL 托管实例和必需的网络配置，但无法向其他人授予访问权限。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 添加指标 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 读取指标定义 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL 安全管理器

允许管理 SQL 服务器和数据库的安全相关策略，但不允许访问它们。 [了解详细信息](../sql-database/sql-database-advanced-data-security.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 不可发出警报。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingPolicies/* | 创建和管理 SQL 服务器审核策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | 创建和管理 SQL 服务器审核设置 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | 检索在给定服务器上配置的扩展服务器 blob 审核策略的详细信息 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingPolicies/* | 创建和管理 SQL 服务器数据库审核策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | 创建和管理 SQL 服务器数据库审核设置 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 检索数据库 Blob 审核记录 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/connectionPolicies/* | 创建和管理 SQL 服务器数据库连接策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | 创建和管理 SQL 服务器数据库数据屏蔽策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | 检索在给定的数据库上配置的扩展 blob 审核策略的详细信息 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | 返回数据库的列表，或获取指定数据库的属性。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | 获取数据库架构。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | 获取数据库列。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | 获取数据库表。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | 创建和管理 SQL 服务器数据库安全警报策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | 创建和管理 SQL 服务器数据库安全度量值 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | 创建和管理 SQL 服务器安全警报策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server 参与者

允许管理 SQL Server 和数据库，但不允许访问它们及其安全相关策略。 [了解详细信息](../sql-database/sql-database-aad-authentication-configure.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | 创建和管理 SQL 服务器 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 添加指标 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 读取指标定义 |
> | **不操作** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingPolicies/* | 编辑 SQL 服务器审核策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | 编辑 SQL 服务器审核设置 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingPolicies/* | 编辑 SQL 服务器数据库审核策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | 编辑 SQL 服务器数据库审核设置 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 检索数据库 Blob 审核记录 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/connectionPolicies/* | 编辑 SQL 服务器数据库连接策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | 编辑 SQL 服务器数据库数据屏蔽策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | 编辑 SQL 服务器数据库安全警报策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | 编辑 SQL 服务器数据库安全度量值 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | 编辑 SQL 服务器安全警报策略 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>分析


### <a name="azure-event-hubs-data-owner"></a>Azure 事件中心数据所有者

允许完全访问 Azure 事件中心资源。 [了解详细信息](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure 事件中心数据接收方

允许接收对 Azure 事件中心资源的访问权限。 [了解详细信息](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure 事件中心数据发送方

允许以发送方式访问 Azure 事件中心资源。 [了解详细信息](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>数据工厂参与者

创建和管理数据工厂，以及其中的子资源。 [了解详细信息](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | 创建和管理数据工厂，以及它们包含的子资源。 |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | 创建和管理数据工厂，以及它们包含的子资源。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | 创建或更新事件订阅 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>数据清除程序

可以清除分析数据 [了解详细信息](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | 从 Application Insights 清除数据 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 查看日志分析数据 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | 从工作区中删除指定数据 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight 群集操作员

允许你读取和修改 HDInsight 群集配置。 [了解详细信息](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | 获取 HDInsight 群集的网关设置 |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | 更新 HDInsight 群集的网关设置 |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 获取或列出部署操作。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight 域服务参与者

可以阅读、创建、修改和删除 HDInsight 所需的域服务相关操作企业安全性套餐 [了解详细信息](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics 参与者

Log Analytics 参与者可以读取所有监视数据并编辑监视设置。 编辑监视设置包括向 VM 添加 VM 扩展、读取存储帐户密钥以便能够从 Azure 存储配置日志收集、创建和配置自动化帐户、添加解决方案以及配置所有 Azure 资源上的 Azure 诊断。 [了解详细信息](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | 安装或更新 Azure Arc 扩展 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 创建、更新或读取 Analysis Server 的诊断设置 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics 读者

Log Analytics 读者可以查看和搜索所有监视数据并查看监视设置，其中包括查看所有 Azure 资源上的 Azure 诊断的配置。 [了解详细信息](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新引擎进行搜索。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 执行搜索查询 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>区块链


### <a name="blockchain-member-node-access-preview"></a>区块链成员节点访问（预览版）

允许访问区块链成员节点 [了解更多](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | 获取或列出现有的区块链成员事务节点。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | 连接到区块链成员事务节点。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + 机器学习


### <a name="cognitive-services-contributor"></a>认知服务参与者

允许创建、读取、更新、删除和管理认知服务的密钥。 [了解详细信息](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | 获取订阅的功能。 |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | 获取给定资源提供程序中某个订阅的功能。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 创建、更新或读取 Analysis Server 的诊断设置 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 读取日志定义 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 读取指标定义 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 添加指标 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 获取或列出部署操作。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>认知服务数据读取者（预览版）

允许读取认知服务数据。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>认知服务用户

允许读取和列出认知服务的密钥。 [了解详细信息](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | 列出密钥 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 读取经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | 读取资源诊断设置 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 读取日志定义 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 读取指标定义 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 添加指标 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 获取或列出部署操作。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>混合现实


### <a name="remote-rendering-administrator"></a>远程渲染管理员

为用户提供 Azure 远程呈现的转换、管理会话、呈现和诊断功能 [了解详细信息](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | 启动资产转换 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | 获取资产转换属性 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | 停止资产转换 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 获取会话属性 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 启动会话 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 停止会话 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 连接到会话 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | 连接到远程渲染检查器 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>远程渲染客户端

为用户提供 Azure 远程渲染的管理会话、渲染和诊断功能。 [了解详细信息](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 获取会话属性 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 启动会话 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 停止会话 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 连接到会话 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | 连接到远程渲染检查器 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>空间定位点帐户参与者

允许你管理帐户中的空间锚，但不能删除它们 [了解更多](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 创建空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 发现附近的空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 获取空间定位点的属性 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 查找空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | 提交诊断数据以帮助提高 Azure 空间定位点服务的质量 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 更新空间定位点属性 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>空间定位点帐户所有者

允许你管理帐户中的空间锚，包括删除它们 [了解更多](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 创建空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | 删除空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 发现附近的空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 获取空间定位点的属性 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 查找空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | 提交诊断数据以帮助提高 Azure 空间定位点服务的质量 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 更新空间定位点属性 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>空间定位点帐户读取者

允许您在帐户中查找和读取空间锚的属性 [了解详细信息](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 发现附近的空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 获取空间定位点的属性 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 查找空间定位点 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | 提交诊断数据以帮助提高 Azure 空间定位点服务的质量 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>集成


### <a name="api-management-service-contributor"></a>API 管理服务参与者

可以管理服务和 API [了解详细信息](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | 创建和管理 API 管理服务 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API 管理服务操作员角色

可以管理服务，但不能管理 API [了解详细信息](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | 读取 API 管理服务实例 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | 将 API 管理服务备份到用户提供的存储帐户中的指定容器 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | 删除 API 管理服务实例 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | 更改 API 管理服务的 SKU/单位，以及添加/删除其区域部署 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | 读取 API 管理服务实例的元数据 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | 从用户提供的存储帐户中的指定容器还原 API 管理服务 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | 上传 API 管理服务的 TLS/SSL 证书 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | 设置、更新或删除 API 管理服务的自定义域名 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | 创建或更新 API 管理服务实例 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 获取与用户关联的密钥 |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API 管理服务读者角色

对服务和 API 的只读访问权限 [了解详细信息](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | 读取 API 管理服务实例 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | 读取 API 管理服务实例的元数据 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 获取与用户关联的密钥 |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>应用程序配置数据所有者

允许对应用程序配置数据进行完全访问。 [了解详细信息](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>应用程序配置数据读取者

允许对应用程序配置数据进行读取访问。 [了解详细信息](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure 服务总线数据所有者

允许完全访问 Azure 服务总线资源。 [了解详细信息](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure 服务总线数据接收方

允许对 Azure 服务总线资源进行接收访问。 [了解详细信息](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure 服务总线数据发送方

允许对 Azure 服务总线资源进行发送访问。 [了解详细信息](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack 注册所有者

允许管理 Azure Stack 注册。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | 获取 Azure Stack Edge 订阅的属性 |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | 获取 Azure Stack 市场产品的属性 |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | 获取 Azure Stack 注册的属性 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription 参与者

可以管理 EventGrid 事件订阅操作。 [了解详细信息](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 按主题类型列出全局事件订阅 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 列出区域事件订阅 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 按主题类型列出区域事件订阅 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription 读者

可以读取 EventGrid 事件订阅。 [了解详细信息](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | 读取事件订阅 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 按主题类型列出全局事件订阅 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 列出区域事件订阅 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 按主题类型列出区域事件订阅 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHIR 数据参与者

角色允许用户或主体完全访问 FHIR 数据 [了解更多](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR 数据导出者

角色允许用户或主体读取和导出 FHIR 数据 [了解详细信息](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | 读取 FHIR 资源（包括搜索任何带有版本的历史记录）。  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | 导出操作 ($export)。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR 数据读取者

角色允许用户或主体读取 FHIR 数据 [了解更多](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | 读取 FHIR 资源（包括搜索任何带有版本的历史记录）。  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR 数据写入者

角色允许用户或主体读取和写入 FHIR 数据 [了解详细信息](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | 硬删除（包括版本历史记录）。 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>集成服务环境参与者

允许管理集成服务环境，但不允许访问这些环境。 [了解详细信息](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>集成服务环境开发人员

允许开发人员在集成服务环境中创建和更新工作流、集成帐户与 API 连接。 [了解详细信息](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | 读取集成服务环境。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/join/action | 加入集成服务环境。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems 帐户参与者

允许管理智能系统帐户，但不允许访问这些帐户。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | Microsoft.IntelligentSystems/accounts/* | 创建和管理智能系统帐户 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>逻辑应用参与者

允许管理逻辑应用，但不允许更改其访问权限。 [了解详细信息](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 返回包含给定帐户的存储帐户。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 创建、更新或读取 Analysis Server 的诊断设置 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | 此权限对于需要通过门户访问活动日志的用户是必需的。 列出活动日志中的日志类别。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | 读取指标定义（资源的可用指标类型的列表）。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | 管理逻辑应用资源。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 返回指定存储帐户的访问密钥。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | 创建和管理连接网关。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | 创建和管理连接。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | 创建和管理自定义 API。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | 加入应用服务计划 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | 获取应用服务计划的属性 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | 列出函数机密。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>逻辑应用运算符

允许读取、启用和禁用逻辑应用，但不允许编辑或更新它们。 [了解详细信息](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | 读取 Insights 警报规则 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | 获取逻辑应用的诊断设置 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | 获取逻辑应用的可用指标。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | 读取逻辑应用资源。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | 禁用工作流。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | 启用工作流。 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | 验证工作流。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 获取或列出部署操作。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | 读取连接网关。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | 读取连接。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | 读取自定义 API。 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | 获取应用服务计划的属性 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>标识


### <a name="managed-identity-contributor"></a>托管的标识参与者

创建、读取、更新和删除用户分配的标识 [了解详细信息](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | 获取现有用户分配标识 |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | 创建新的用户分配标识或更新与现有用户分配标识关联的标记 |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | 删除现有用户分配标识 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>托管的标识操作员

读取和分配用户分配的标识 [了解详细信息](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>安全性


### <a name="azure-sentinel-contributor"></a>Azure Sentinel 参与者

Azure Sentinel 参与者 [了解更多](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新引擎进行搜索。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 查看日志分析数据 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 获取现有的 OMS 解决方案 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 对工作区中的数据运行查询 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 获取工作区下面的数据源。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel 读取者

Azure Sentinel 读者 [了解更多](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 检查用户授权和许可证 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新引擎进行搜索。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 查看日志分析数据 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | 获取给定工作区下的链接服务。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 获取保存的搜索查询 |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 获取现有的 OMS 解决方案 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 对工作区中的数据运行查询 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 获取工作区下面的数据源。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 读取工作簿 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel 响应方

Azure Sentinel 响应程序 [了解详细信息](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 检查用户授权和许可证 |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新引擎进行搜索。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 查看日志分析数据 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 获取工作区下面的数据源。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 获取保存的搜索查询 |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 获取现有的 OMS 解决方案 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 对工作区中的数据运行查询 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 获取工作区下面的数据源。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 读取工作簿 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator-preview"></a>Key Vault 管理员 (预览版) 

对密钥保管库以及其中的所有对象（包括证书、密钥和机密）执行所有数据平面操作。 无法管理 key vault 资源或管理角色分配。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 检查密钥保管库名称是否有效且未被使用 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 查看软删除的密钥保管库的属性 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可对 Microsoft.KeyVault 资源提供程序执行的操作 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a> (预览版 Key Vault 证书官员) 

对密钥保管库的证书执行任何操作（管理权限除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 检查密钥保管库名称是否有效且未被使用 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 查看软删除的密钥保管库的属性 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可对 Microsoft.KeyVault 资源提供程序执行的操作 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>密钥保管库参与者

管理密钥保管库，但不允许在 Azure RBAC 中分配角色，而且不允许你访问机密、密钥或证书。 [了解详细信息](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | 清除软删除的密钥保管库 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer-preview"></a> (预览版 Key Vault 加密官) 

对密钥保管库的密钥执行任何操作（"管理权限" 除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 检查密钥保管库名称是否有效且未被使用 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 查看软删除的密钥保管库的属性 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可对 Microsoft.KeyVault 资源提供程序执行的操作 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-preview"></a>Key Vault 加密服务加密 (预览版) 

读取密钥的元数据并执行包装/解包操作。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 列出指定保管库中的密钥，或读取密钥的属性和公共材料。 对于非对称密钥，此操作会公开公钥，并提供执行公钥算法（例如加密和验证签名）的功能。 永远不会公开私钥和对称密钥。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | 使用 Key Vault 密钥包装对称密钥。 请注意，如果 Key Vault 密钥为非对称密钥，则可以使用读取访问权限执行此操作。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | 使用 Key Vault 密钥解包对称密钥。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Key Vault 加密用户 (预览版) 

使用密钥执行加密操作。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 列出指定保管库中的密钥，或读取密钥的属性和公共材料。 对于非对称密钥，此操作会公开公钥，并提供执行公钥算法（例如加密和验证签名）的功能。 永远不会公开私钥和对称密钥。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | 更新与给定密钥关联的指定属性。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | 创建密钥的备份文件。 该文件可用于还原同一订阅的 Key Vault 中的密钥。 可能存在限制。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | 使用密钥加密纯文本。 请注意，如果密钥为非对称密钥，此操作可以由拥有读取访问权限的主体执行。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | 使用密钥解密加密文本。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | 使用 Key Vault 密钥包装对称密钥。 请注意，如果 Key Vault 密钥为非对称密钥，则可以使用读取访问权限执行此操作。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | 使用 Key Vault 密钥解包对称密钥。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | 使用密钥签名哈希。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | 验证哈希。 请注意，如果密钥为非对称密钥，此操作可以由拥有读取访问权限的主体执行。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Key Vault Reader (预览版) 

读取密钥保管库及其证书、密钥和机密的元数据。 无法读取机密内容或密钥材料之类的敏感值。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 检查密钥保管库名称是否有效且未被使用 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 查看软删除的密钥保管库的属性 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可对 Microsoft.KeyVault 资源提供程序执行的操作 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 列出或查看机密的属性，但不列出或查看机密的值。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a> (预览版 Key Vault 秘密专员) 

对密钥保管库的密码执行任何操作（"管理权限" 除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 检查密钥保管库名称是否有效且未被使用 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 查看软删除的密钥保管库的属性 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可对 Microsoft.KeyVault 资源提供程序执行的操作 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Key Vault 机密用户 (预览版) 

读取机密内容。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | 获取机密值。 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 列出或查看机密的属性，但不列出或查看机密的值。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>安全管理员

查看和更新安全中心的权限。 与安全读取者角色具有相同的权限，还可以更新安全策略并关闭警报和建议。 [了解详细信息](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | 创建和管理策略分配 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | 创建和管理策略定义 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | 创建和管理策略集 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 查看日志分析数据 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | 创建和管理安全组件和策略 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>安全评估参与者

允许你将评估推送到安全中心

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | 创建或更新订阅的安全评估 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>安全管理器（旧版）

这是旧角色。 请改用安全管理员。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | 读取经典虚拟机的配置信息 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | 写入经典虚拟机的配置 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | 读取有关经典网络的配置信息 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | 创建和管理安全组件和策略 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>安全读取者

查看安全中心的权限。 可以查看但不能更改建议、警报、安全策略和安全状态。 [了解详细信息](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 读取经典指标警报 |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 查看日志分析数据 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | 读取安全组件和策略 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest 实验室用户

允许连接、启动、重启和关闭 Azure 开发测试实验室中的虚拟机。 [了解详细信息](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | 获取可用性集的属性 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | 读取虚拟机属性（VM 大小、运行时状态、VM 扩展等） |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | 关闭虚拟机并释放计算资源 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 获取虚拟机的属性 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | 重新启动虚拟机 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | 启动虚拟机 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | 读取实验室属性 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | 在实验室中声明随机可声明的虚拟机。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | 在实验室中创建虚拟机。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | 确保当前用户在实验室中存在有效的配置文件。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | 删除公式。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | 读取公式。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | 添加或修改公式。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | 评估实验室策略。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | 获得现有虚拟机的所有权 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | 列出适用的启动/停止计划（如果有）。 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | 获取一个字符串，该字符串表示虚拟机的 RDP 文件内容 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 加入负载均衡器后端地址池。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 加入负载均衡器入站 NAT 规则。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | 读取网络接口（例如，此网络接口所属的所有负载均衡器）的属性 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | 将虚拟机加入到网络接口。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 获取网络接口定义。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 创建网络接口，或更新现有的网络接口。  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | 读取公共 IP 地址的属性 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 加入公共 IP 地址。 不可发出警报。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虚拟网络。 不可发出警报。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 获取或列出部署操作。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | 获取或列出部署。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | **不操作** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | 列出可将虚拟机更新到的大小 |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>实验室创建者

允许在 Azure 实验室帐户下新建实验室。 [了解详细信息](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | 在实验室帐户中创建实验室。 |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | 获取实验室帐户的大小、地理位置和操作系统组合的定价与可用性。 |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | 获取此订阅的核心限制和用量 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>监视


### <a name="application-insights-component-contributor"></a>Application Insights 组件参与者

可以管理 Application Insights 组件 [了解详细信息](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典警报规则 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | 创建和管理新警报规则 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | 创建和管理 Insights 组件 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | 创建和管理 Insights Web 测试 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights 快照调试器

授予用户查看和下载使用 Application Insights Snapshot Debugger 收集的调试快照的权限。 请注意，[所有者](#owner)或[参与者](#contributor)角色不包括这些权限。 在向用户授予 Application Insights Snapshot Debugger 角色时，必须将该角色直接授予用户。 将角色添加到自定义角色时，无法识别该角色。 [了解详细信息](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>监视参与者

可以读取所有监视数据和编辑监视设置。 另请参阅 [Azure Monitor 的角色、权限和安全入门](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)。 [了解详细信息](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | 创建和管理 Insights 组件 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | 创建、更新或读取 Analysis Server 的诊断设置 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | 列出订阅中的活动日志事件（管理事件）。 此权限适用于以编程方式和通过门户访问活动日志。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | 此权限对于需要通过门户访问活动日志的用户是必需的。 列出活动日志中的日志类别。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | 读取指标定义（资源的可用指标类型的列表）。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | 读取资源的指标。 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | 注册 Microsoft Insights 提供程序 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | 创建和管理 Insights Web 测试 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | 创建新的工作区，或者通过提供现有工作区中的客户 ID 链接到现有工作区。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | 读取/写入/删除日志分析解决方案包。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | 读取/写入/删除日志分析保存的搜索。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 执行搜索查询 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | 读取/写入/删除日志分析存储见解配置。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* |  |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/notificationSettings/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>监视指标发布者

允许针对 Azure 资源发布指标 [了解详细信息](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | 注册 Microsoft Insights 提供程序 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | 写入指标 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>监视查阅者

可以读取所有监视数据（指标、日志等）。 另请参阅 [Azure Monitor 的角色、权限和安全入门](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)。 [了解详细信息](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 执行搜索查询 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>工作簿参与者

可以保存共享的工作簿。 [了解详细信息](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | 创建或更新工作簿 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | 删除工作簿 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 读取工作簿 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>工作簿读者

可以读取工作簿。 [了解详细信息](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 读取工作簿 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>管理 + 治理


### <a name="automation-job-operator"></a>自动化作业操作员

使用自动化 Runbook 创建和管理作业。 [了解详细信息](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | 读取混合 Runbook 辅助角色资源 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | 获取 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | 恢复 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | 停止 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | 暂停 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | 创建 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | 获取作业的输出 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>自动化运算符

自动化操作员能够启动、停止、暂停和恢复作业 [了解详细信息](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | 读取混合 Runbook 辅助角色资源 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | 获取 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | 恢复 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | 停止 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | 暂停 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | 创建 Azure 自动化作业 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | 获取 Azure 自动化作业计划 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | 创建 Azure 自动化作业计划 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | 获取链接到自动化帐户的工作区 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | 获取 Azure 自动化帐户 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | 获取 Azure 自动化 Runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | 获取 Azure 自动化计划资产 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | 创建或更新 Azure 自动化计划资产 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | 获取作业的输出 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>自动化 Runbook 操作员

读取 Runbook 属性 - 以能够创建 runbook 的作业。 [了解详细信息](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | 获取 Azure 自动化 Runbook |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine 加入

可以加入 Azure Connected Machine。 [了解详细信息](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | 读取任何 Azure Arc 计算机 |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | 写入 Azure Arc 计算机 |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | 获取来宾配置分配。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine 资源管理员

可以读取、写入、删除和重新加入 Azure Connected Machine。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | 读取任何 Azure Arc 计算机 |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | 写入 Azure Arc 计算机 |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | 删除 Azure Arc 计算机 |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/reconnect/action | 重新连接 Azure Arc 计算机 |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | 安装或更新 Azure Arc 扩展 |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>计费读者

允许对帐单数据进行读取访问 [了解更多](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | 读取计费信息 |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>蓝图参与者

可以管理蓝图定义，但不能对其进行分配。 [了解详细信息](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | 创建和管理蓝图定义或蓝图项目。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>蓝图操作员

可以指定现有已发布的蓝图，但不能创建新的蓝图。 请注意：仅当使用用户分配的托管标识完成分配时，此分配才有效。 [了解详细信息](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | 创建和管理蓝图分配。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>成本管理参与者

可以查看成本和管理成本配置 (例如预算、出口) [了解更多](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 获取配置 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 读取建议 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>成本管理读者

可以查看成本数据和配置 (例如预算、出口) [了解更多](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 获取配置 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 读取建议 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>层次结构设置管理员

允许用户编辑和删除层次结构设置

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | 创建或更新管理组层次结构设置。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | 删除管理组层次结构设置。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes 群集 - Azure Arc 载入

用于授权任何用户/服务创建 connectedClusters 资源的角色定义 [了解详细信息](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 创建或更新部署。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 获取订阅的列表。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | 写入 connectedClusters |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | 读取 connectedClusters |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>托管应用程序参与者角色

允许创建托管应用程序资源。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | 注册到解决方案。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>托管应用程序操作员角色

可让你在托管应用程序资源上读取和执行操作

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | 检索应用程序列表。 |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>托管应用程序读者

允许读取托管应用中的资源并请求 JIT 访问。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>托管服务注册分配删除角色

托管服务注册分配删除角色允许管理租户用户删除分配给其租户的注册分配。 [了解详细信息](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | 检索托管服务注册分配的列表。 |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | 删除托管服务注册分配。 |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | 读取资源的操作状态。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>管理组参与者

管理组参与者角色 [了解详细信息](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | 删除管理组。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | 从管理组取消关联订阅。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | 将现有订阅与管理组关联。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | 创建或更新管理组。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 列出特定管理组下的订阅。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>管理组读取者

管理组读取者角色

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 列出特定管理组下的订阅。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>New elic APM 帐户参与者

允许管理 New Relic 应用程序性能管理帐户和应用程序，但不允许访问它们。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | NewRelic.APM/accounts/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>策略见解数据编写者（预览版）

允许对资源策略进行读取访问，并允许对资源组件策略事件进行写入访问。 [了解详细信息](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | 获取有关策略分配的信息。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | 获取有关策略定义的信息。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | 获取有关策略集定义的信息。 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | 参照数据策略检查给定组件的合规性状态。 |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | 记录资源组件策略事件。 |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>资源策略参与者

有权创建/修改资源策略、创建支持票证和读取资源/层次结构的用户。 [了解详细信息](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | */read | 读取除密码外的所有类型的资源。 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | 创建和管理策略分配 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | 创建和管理策略定义 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | 创建和管理策略集 |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery 参与者

允许管理 Site Recovery 服务，但保管库创建和角色分配除外 [了解详细信息](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp 是服务使用的内部操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 创建和管理与保管库相关的扩展信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 创建和管理已注册标识 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | 创建或更新复制警报设置 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 读取任何事件 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | 创建和管理复制结构 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 创建和管理复制作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | 创建和管理复制策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | 创建和管理恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | 创建和管理恢复服务保管库的存储配置 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | 读取恢复服务保管库的警报 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | 读取任何保管库复制操作状态 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery 操作员

允许进行故障转移和故障回复，但不允许执行其他 Site Recovery 管理操作 [了解详细信息](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 获取虚拟网络定义 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp 是服务使用的内部操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 读取任何警报设置 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 读取任何事件 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | 检查结构的一致性 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 读取任何结构 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | 重新关联网关 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | 续订 Fabric 的证书 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 读取任何网络 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 读取任何网络映射 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 读取任何保护容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 读取任何可保护项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 应用还原点 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 故障转移提交 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 计划内故障转移 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 读取任何受保护项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 读取任何复制恢复点 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修复复制 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保护受保护的项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 交换保护容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 测试故障转移 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 测试故障转移清理 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 故障转移 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新移动服务 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 刷新提供程序 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 读取任何存储分类 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | 读取任何 vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 创建和管理复制作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 读取任何策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | 故障转移提交恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | 计划内故障转移恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 读取任何恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | 重新保护恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | 测试故障转移恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 测试故障转移清理恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | 故障转移恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | 读取恢复服务保管库的警报 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery 读取者

允许查看 Site Recovery 状态，但不允许执行其他管理操作 [了解详细信息](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象扩展信息 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 读取任何警报设置 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 读取任何事件 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 读取任何结构 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 读取任何网络 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 读取任何网络映射 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 读取任何保护容器 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 读取任何可保护项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 读取任何受保护项 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 读取任何复制恢复点 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 读取任何存储分类 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | 读取任何 vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/rea | 读取任何作业 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 读取任何策略 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 读取任何恢复计划 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>支持请求参与者

允许创建和管理支持请求 [了解详细信息](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>标记参与者

允许你管理实体上的标记，而无需提供对实体本身的访问权限。 [了解详细信息](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | 获取资源组的资源。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | 获取订阅的资源。 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>其他


### <a name="biztalk-contributor"></a>BizTalk 参与者

允许管理 BizTalk 服务，但不允许访问这些服务。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | Microsoft.BizTalkServices/BizTalk/* | 创建和管理 BizTalk 服务 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>桌面虚拟化用户

允许用户使用应用程序组中的应用程序。 [了解详细信息](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | *无* |  |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | Microsoft.DesktopVirtualization/applicationGroups/useApplications/action | 使用 ApplicationGroup |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>计划程序作业集合参与者

允许管理计划程序作业集合，但不允许访问这些集合。

> [!div class="mx-tableFixed"]
> | 操作 | 描述 |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 读取角色和角色分配 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 创建和管理经典指标警报 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 创建和管理部署 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | 创建和管理作业集合 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 创建和更新支持票证 |
> | **不操作** |  |
> | *无* |  |
> | **DataActions** |  |
> | *无* |  |
> | **NotDataActions** |  |
> | *无* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>后续步骤

- [将资源提供程序与服务匹配](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 自定义角色](custom-roles.md)
- [Azure 安全中心中的权限](../security-center/security-center-permissions.md)
