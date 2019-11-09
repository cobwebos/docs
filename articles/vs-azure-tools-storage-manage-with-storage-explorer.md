---
title: 存储资源管理器入门 | Microsoft Docs
description: 利用存储资源管理器来管理 Azure 存储资源
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891235"
---
# <a name="get-started-with-storage-explorer"></a>存储资源管理器入门

## <a name="overview"></a>概述

Microsoft Azure 存储资源管理器是一个独立的应用，使用它可在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 本文介绍如何通过多种方式连接到 Azure 存储帐户并对其进行管理。

![Microsoft Azure 存储资源管理器][0]

## <a name="prerequisites"></a>必备组件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

以下版本的 Windows 支持存储资源管理器：

* Windows 10（推荐）
* Windows 8
* Windows 7

对于所有版本的 Windows，存储资源管理器需要 .NET Framework 4.6.2 或更高版本。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

以下版本的 macOS 支持存储资源管理器：

* macOS 10.12 塞拉利昂和更高版本

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

对于最常见的 Linux 发行版，在[Snap 店](https://snapcraft.io/storage-explorer)中提供存储资源管理器。 对于此安装，我们建议 Snap Store。 当新版本发布到 Snap Store 时，存储资源管理器 snap 会安装其所有依赖项和更新。

有关支持的分发版，请参阅[snapd 安装页](https://snapcraft.io/docs/installing-snapd)。

存储资源管理器要求使用密码管理器。 您可能必须手动连接到密码管理器。 可以运行以下命令，将存储资源管理器连接到系统的密码管理器：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

存储资源管理器也可作为*gz*下载。 需要手动安装依赖项。 Linux 支持*gz*安装的以下分发版：

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

*Gz*安装可在其他发行版上运行，但仅正式支持列出的版本。

有关在 Linux 上安装存储资源管理器的详细信息，请参阅 Azure 存储资源管理器故障排除指南中的[Linux 依赖项](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

---

## <a name="download-and-install"></a>下载并安装

若要下载并安装存储资源管理器，请参阅[Azure 存储资源管理器](https://www.storageexplorer.com)。

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务

存储资源管理器提供了多种连接到存储帐户的方式。 一般情况下，你可以：

* [登录到 Azure 以访问自己的订阅及其资源](#sign-in-to-azure)
* [附加特定的存储或 CosmosDB 资源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登录 Azure

> [!NOTE]
> 登录后，存储资源管理器要求拥有管理（Azure 资源管理器）和数据层权限才能全面访问资源。 这意味着，你需要拥有 Azure Active Directory (Azure AD) 权限，可以访问自己的存储帐户、该帐户中的容器以及该容器中的数据。 如果你仅在数据层拥有权限，请考虑[通过 Azure AD 添加资源](#add-a-resource-via-azure-ad)。 有关存储资源管理器要求的特定权限的详细信息，请参阅[Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)。

1. 在存储资源管理器中，选择 "**查看** > **帐户管理**"，或选择 "**管理帐户**" 按钮。

    ![管理帐户][1]

1. **帐户管理**现在显示你已登录到的所有 Azure 帐户。 若要连接到另一个帐户，请选择“添加帐户”。

1. 在 "**连接到 Azure 存储**" 中，从**azure 环境**中选择一个 azure 云，以便登录到全国云或 Azure Stack。 选择环境后，选择 "**下一步**"。

    ![用于登录的选项][2]

    存储资源管理器将打开一个用于登录的页面。 有关详细信息，请参阅[将存储资源管理器连接到 Azure Stack 订阅或存储帐户](/azure-stack/user/azure-stack-storage-connect-se)。

1. 使用 Azure 帐户成功登录后，将在 "**帐户管理**" 下显示与该帐户关联的帐户和 Azure 订阅。 选择 "**所有订阅**" 可在所有列出的 Azure 订阅之间切换所选内容。 选择要使用的 Azure 订阅，并选择“应用”。

    ![选择 Azure 订阅][3]

    **资源管理器**显示与所选 Azure 订阅关联的存储帐户。

    ![选择的 Azure 订阅][4]

### <a name="attach-a-specific-resource"></a>附加特定的资源

可以通过多种方式在存储资源管理器中附加到资源：

* [通过 Azure AD 添加资源](#add-a-resource-via-azure-ad)。 如果你仅在数据层拥有权限，请使用此选项添加 Blob 容器或 Azure Data Lake Storage Gen2 Blob 存储容器。
* [使用连接字符串](#use-a-connection-string)。 如果已获取存储帐户的连接字符串，请使用此选项。 存储资源管理器支持密钥和[共享访问签名](storage/common/storage-dotnet-shared-access-signature-part-1.md)连接字符串。
* [使用共享访问签名 URI](#use-a-shared-access-signature-uri)。 如果有一个 blob 容器、文件共享、队列或表的[共享访问签名 URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) ，请使用它附加到资源。 若要获取共享访问签名 URI，可以使用[存储资源管理器](#generate-a-sas-in-storage-explorer)或[Azure 门户](https://portal.azure.com)。
* [使用名称和密钥](#use-a-name-and-key)。 如果你知道存储帐户的任一帐户密钥，则可以使用此选项快速建立连接。 在 "存储帐户" 页中，通过选择 "**设置**" > " [Azure 门户](https://portal.azure.com)中的"**访问密钥**"查找密钥。
* [附加到本地仿真器](#attach-to-a-local-emulator)。 如果你正在使用某个可用的 Azure 存储仿真器，则使用此选项可以轻松连接到该仿真器。
* [使用连接字符串连接到 Azure Cosmos DB 帐户](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 如果已获取 CosmosDB 实例的连接字符串，请使用此选项。
* [通过 URI 连接到 Azure Data Lake Store](#connect-to-azure-data-lake-store-by-uri)。 如果已获取 Azure Data Lake Store 的 URI，请使用此选项。

#### <a name="add-a-resource-via-azure-ad"></a>通过 Azure AD 添加资源

1. 选择 "**连接**" 符号，打开 "**连接到 Azure 存储**"。

    ![“连接到 Azure 存储”选项][9]

1. 使用“添加 Azure 帐户”选项登录到有权访问资源的 Azure 帐户（如果尚未执行此操作）。 登录后，返回以**连接到 Azure 存储**。

1. 依次选择“通过 Azure Active Directory (Azure AD)添加资源”、“下一步”。

1. 选择 "Azure 帐户" 和 "租户"。 这些值必须具有对要附加到的存储资源的访问权限。 选择“**下一步**”。

1. 选择要附加的资源类型。 输入连接所需的信息。 

   在此页上输入的信息取决于要添加的资源类型。 请确保选择正确的资源类型。 输入所需的信息后，选择“下一步”。

1. 查看**连接摘要**，确保所有信息均正确无误。 如果正确，请选择“连接”。 否则，选择 "**上一步**" 返回到前一页，修复所有不正确的信息。

成功添加连接后，资源树将转到表示连接的节点。 资源显示在 "**本地 &** " 下，附加 > **存储帐户** >  **（附加容器）**  > **Blob 容器**。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-connection-string"></a>使用连接字符串

1. 选择 "**连接**" 符号，打开 "**连接到 Azure 存储**"。

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“使用连接字符串”、“下一步”。

1. 选择连接的显示名称，并输入连接字符串。 然后，选择“下一步”。

1. 查看**连接摘要**，确保所有信息均正确无误。 如果正确，请选择“连接”。 否则，选择 "**上一步**" 返回到前一页，修复所有不正确的信息。

成功添加连接后，资源树将转到表示连接的节点。 资源显示在 "**本地 & 附加** > **存储帐户**" 下。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-shared-access-signature-uri"></a>使用共享访问签名 URI

1. 选择 "**连接**" 符号，打开 "**连接到 Azure 存储**"。

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“使用共享访问签名(SAS) URI”、“下一步”。

1. 为连接选择一个显示名称，然后输入共享访问签名 URI。 适用于要附加的资源类型的服务终结点应会自动填充。 如果使用的是自定义终结点，有可能不会填充该终结点。 选择“**下一步**”。

1. 查看**连接摘要**，确保所有信息均正确无误。 如果正确，请选择“连接”。 否则，选择 "**上一步**" 返回到前一页，修复所有不正确的信息。

成功添加连接后，资源树将转到表示连接的节点。 资源会显示在 "**本地 &** " 下，附加 > **存储帐户**" >  **（附加容器）**  > 已*附加容器类型的" 服务 "节点*。 如果存储资源管理器无法添加你的连接，请参阅[Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。 如果你在成功添加连接后无法访问你的数据，请参阅故障排除指南。

#### <a name="use-a-name-and-key"></a>使用名称和密钥

1. 选择 "**连接**" 符号，打开 "**连接到 Azure 存储**"。

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“使用存储帐户名和密钥”、“下一步”。

1. 选择连接的显示名称。

1. 输入存储帐户名称及其任一访问密钥。

1. 选择要使用的**存储域**，然后选择“下一步”。

1. 查看**连接摘要**，确保所有信息均正确无误。 如果正确，请选择“连接”。 否则，选择 "**上一步**" 返回到前一页，修复所有不正确的信息。

成功添加连接后，资源树将转到表示连接的节点。 资源显示在 "**本地 & 附加** > **存储帐户**" 下。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="attach-to-a-local-emulator"></a>附加到本地仿真器

存储资源管理器目前支持两个官方存储仿真器：

* [Azure 存储仿真器](storage/common/storage-use-emulator.md)（仅适用于 Windows）
* [Azurite](https://github.com/azure/azurite)（Windows、macOS 或 Linux）

如果模拟器正在侦听默认端口，则可以使用**模拟器默认端口**节点访问模拟器。 在 "**本地 &** " 下 > **存储帐户**"下查找**模拟器默认端口**。

若要为连接使用不同的名称，或者仿真器不是在默认端口上运行，请执行以下步骤：

1. 启动仿真器。 输入命令 `AzureStorageEmulator.exe status` 显示每种服务类型的端口。

   > [!IMPORTANT]
   > 存储资源管理器不会自动启动你的仿真器。 必须手动启动它。

1. 选择 "**连接**" 符号，打开 "**连接到 Azure 存储**"。

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“附加到本地仿真器”、“下一步”。

1. 选择连接的显示名称，并输入每种服务类型的仿真器所侦听的端口。 **附加到本地模拟器**会为大多数模拟器推荐默认端口值。 **文件端口**为空白，因为这两个官方仿真器当前均不支持文件服务。 如果正在使用的模拟器支持文件，可以输入要使用的端口。 然后，选择“下一步”。

1. 查看**连接摘要**，确保所有信息均正确。 如果正确，请选择“连接”。 否则，选择 "**上一步**" 返回到前一页，修复所有不正确的信息。

成功添加连接后，资源树将转到表示连接的节点。 节点应出现在 "**本地 &** " 下，附加 > **存储帐户**。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB 帐户

可以使用连接字符串连接到 Azure Cosmos DB，而不是通过 Azure 订阅管理 Azure Cosmos DB 帐户。 若要连接，请执行以下步骤：

1. 在 "**资源管理器**" 下，展开 "**本地 & 附加**"，右键单击**Cosmos DB 帐户**"，然后选择"**连接到 Azure Cosmos DB**"。

    ![通过连接字符串连接到 Azure Cosmos DB][21]

1. 选择“Azure Cosmos DB API”，输入“连接字符串”数据，然后选择“确定”以连接到 Azure Cosmos DB 帐户。 有关如何检索连接字符串的信息，请参阅[管理 Azure Cosmos 帐户](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![连接字符串][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>通过 URI 连接到 Azure Data Lake Store

可以访问不在订阅中的资源。 你需要有权访问该资源的人员提供资源 URI。 登录后，使用 URI 连接到 Data Lake Store。 若要连接，请执行以下步骤：

1. 在**资源管理器**下，展开 "**本地 & 附加**"。

1. 右键单击**Data Lake Storage Gen1**，然后选择 "**连接到 Data Lake Storage Gen1**"。

    ![“连接到 Data Lake Store”上下文菜单](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. 输入 URI，然后选择 **"确定"** 。 Data Lake Store 显示在 " **Data Lake Storage**下。

    ![“连接到 Data Lake Store”操作结果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

此示例使用 Data Lake Storage Gen1。 Azure Data Lake Storage Gen2 现已可用。 有关详细信息，请参阅[什么是 Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md)。

## 在存储资源管理器中生成共享访问签名<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>帐户级别共享访问签名

1. 右键单击要共享的存储帐户，并选择“获取共享访问签名”。

    !["获取共享访问签名" 上下文菜单选项][14]

1. 在 "**共享访问签名**" 中，为帐户指定所需的时间范围和权限，然后选择 "**创建**"。

    ![获取共享访问签名][15]

1. 将**连接字符串**或原始**查询字符串**复制到剪贴板。

### <a name="service-level-shared-access-signature"></a>服务级别共享访问签名

你可以在服务级别获取共享访问签名。 有关详细信息，请参阅[获取 blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。

## <a name="search-for-storage-accounts"></a>搜索存储帐户

若要查找存储资源，可以在 "**资源管理器**" 窗格中进行搜索。

当您在 "搜索" 框中输入文本时，存储资源管理器将显示与您在该点之前输入的搜索值匹配的所有资源。 此示例演示如何搜索**终结点**：

![存储帐户搜索][23]

> [!NOTE]
> 若要加快搜索速度，请使用**帐户管理**取消选择不包含所要搜索的项的订阅。 还可以右键单击某个节点，然后选择“从此处搜索”，从特定的节点开始搜索。
>
>

## <a name="next-steps"></a>后续步骤

* [利用存储资源管理器管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
* [使用 Azure 存储资源管理器处理数据](./cosmos-db/storage-explorer.md)
* [使用存储资源管理器管理 Azure Data Lake Store 资源](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
