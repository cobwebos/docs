---
title: 存储资源管理器入门 | Microsoft Docs
description: 开始使用存储资源管理器管理 Azure 存储资源。 下载并安装 Azure 存储资源管理器、连接到存储帐户或服务，等等。
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 9922601d67393d103b95da7286d8db71d913434a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88235239"
---
# <a name="get-started-with-storage-explorer"></a>存储资源管理器入门

## <a name="overview"></a>概述

Microsoft Azure 存储资源管理器是一个独立的应用，使用它可在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 本文介绍如何通过多种方式连接到 Azure 存储帐户并对其进行管理。

![Microsoft Azure 存储资源管理器][0]

## <a name="prerequisites"></a>先决条件

# <a name="windows"></a>[Windows](#tab/windows)

以下版本的 Windows 支持存储资源管理器：

* Windows 10（推荐）
* Windows 8
* Windows 7

对于所有 Windows 版本，存储资源管理器都至少需要 .NET Framework 4.7.2。

# <a name="macos"></a>[macOS](#tab/macos)

以下版本的 macOS 支持存储资源管理器：

* macOS 10.12 Sierra 和更高版本

# <a name="linux"></a>[Linux](#tab/linux)

最常用 Linux 分发版的 [Snap Store](https://snapcraft.io/storage-explorer) 中都提供了存储资源管理器。 我们建议通过 Snap Store 安装存储资源管理器。 将新版本发布到 Snap Store 时，存储资源管理器内嵌项会安装其所有依赖项和更新。

有关支持的分发版，请参阅 [snapd 安装页](https://snapcraft.io/docs/installing-snapd)。

存储资源管理器要求使用密码管理器。 可能必须手动连接到密码管理器。 可以运行以下命令，将存储资源管理器连接到系统的密码管理器：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

存储资源管理器还以 *.tar.gz* 下载文件格式提供。 必须手动安装依赖项。 以下 Linux 分发版支持 *.tar.gz* 安装：

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*.tar.gz* 安装也许能够在其他分发版上正常进行，但目前只有列出的分发版受到官方支持。

在 Linux 上安装存储资源管理器时如需更多帮助，请参阅 Azure 存储资源管理器故障排除指南中的 [Linux 依赖项](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

---

## <a name="download-and-install"></a>下载并安装

若要下载并安装存储资源管理器，请参阅 [Azure 存储资源管理器](https://www.storageexplorer.com)。

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务

存储资源管理器提供多种连接到存储帐户的方式。 一般情况下，你可以：

* [登录到 Azure 以访问自己的订阅及其资源](#sign-in-to-azure)
* [附加特定的存储或 CosmosDB 资源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登录 Azure

> [!NOTE]
> 登录后，存储资源管理器要求拥有管理（Azure 资源管理器）和数据层权限才能全面访问资源。 这意味着，你需要拥有 Azure Active Directory (Azure AD) 权限，可以访问自己的存储帐户、该帐户中的容器以及该容器中的数据。 如果你仅在数据层拥有权限，请考虑[通过 Azure AD 添加资源](#add-a-resource-via-azure-ad)。 若要详细了解存储资源管理器所需的具体权限，请参阅 [Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)。

1. 在存储资源管理器中，选择“视图” > “帐户管理”，或选择“管理帐户”按钮。  

    ![管理帐户][1]

1. “帐户管理”随即显示已登录到的所有 Azure 帐户。 若要连接到另一个帐户，请选择“添加帐户”。

1. 在“连接到 Azure 存储”中，从“Azure 环境”中选择一个 Azure 云，以登录到国家云或 Azure Stack。  选择环境后，选择“下一步”。

    ![登录选项][2]

    存储资源管理器将打开一个登录页。 有关详细信息，请参阅[将存储资源管理器连接到 Azure Stack 订阅或存储帐户](/azure-stack/user/azure-stack-storage-connect-se)。

1. 使用 Azure 帐户成功登录后，该帐户以及与之关联的 Azure 订阅将显示在“帐户管理”下。 选择“所有订阅”会选择所有列出的 Azure 订阅，或者一个都不选。 选择要使用的 Azure 订阅，并选择“应用”。

    ![选择 Azure 订阅][3]

    “资源管理器”会显示与所选 Azure 订阅关联的存储帐户。

    ![选择的 Azure 订阅][4]

### <a name="attach-a-specific-resource"></a>附加特定的资源

可通过多种方式在存储资源管理器中附加资源：

* [通过 Azure AD 添加资源](#add-a-resource-via-azure-ad)。 如果你仅在数据层拥有权限，请使用此选项添加 Blob 容器或 Azure Data Lake Storage Gen2 Blob 存储容器。
* [使用连接字符串](#use-a-connection-string)。 如果已获取存储帐户的连接字符串，请使用此选项。 存储资源管理器支持密钥和[共享访问签名](storage/common/storage-dotnet-shared-access-signature-part-1.md)连接字符串。
* [使用共享访问签名 URI](#use-a-shared-access-signature-uri)。 如果已获取 Blob 容器、文件共享、队列或表的[共享访问签名 URI](storage/common/storage-dotnet-shared-access-signature-part-1.md)，请使用它附加到资源。 若要获取共享访问签名 URI，可以使用[存储资源管理器](#generate-a-sas-in-storage-explorer)或 [Azure 门户](https://portal.azure.com)。
* [使用名称和密钥](#use-a-name-and-key)。 如果你知道存储帐户的任一帐户密钥，则可以使用此选项快速建立连接。 在 [Azure 门户](https://portal.azure.com)中选择“设置” > “访问密钥”，在存储帐户页中找到你的密钥。 
* [附加到本地仿真器](#attach-to-a-local-emulator)。 如果你正在使用某个可用的 Azure 存储仿真器，则使用此选项可以轻松连接到该仿真器。
* [使用连接字符串连接到 Azure Cosmos DB 帐户](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 如果已获取 CosmosDB 实例的连接字符串，请使用此选项。
* [通过 URI 连接到 Azure Data Lake Store](#connect-to-azure-data-lake-store-by-uri)。 如果已获取 Azure Data Lake Store 的 URI，请使用此选项。

#### <a name="add-a-resource-via-azure-ad"></a>通过 Azure AD 添加资源

1. 选择“连接”符号打开“连接到 Azure 存储”。 

    ![“连接到 Azure 存储”选项][9]

1. 使用“添加 Azure 帐户”选项登录到有权访问资源的 Azure 帐户（如果尚未执行此操作）。 登录后，返回“连接到 Azure 存储”。

1. 依次选择“通过 Azure Active Directory (Azure AD)添加资源”、“下一步”。 

1. 选择 Azure 帐户和租户。 必须可以使用这些值访问要附加到的存储资源。 选择“**下一步**”。

1. 选择要附加的资源类型。 输入用于连接的信息。 

   在此页上输入的信息取决于要添加的资源类型。 请确保选择正确的资源类型。 输入所需的信息后，选择“下一步”。

1. 检查“连接摘要”，确保所有信息正确。 如果正确，请选择“连接”。 否则，请选择“后退”返回前面的页，以修复任何错误的信息。

成功添加连接后，资源树中将打开表示该连接的节点。 该资源显示在“本地和附加” > “存储帐户” > “(附加的容器)” > “Blob 容器”下。    如果存储资源管理器无法添加你的连接，或者在成功添加连接后你无法访问自己的数据，请参阅 [Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-connection-string"></a>使用连接字符串

1. 选择“连接”符号打开“连接到 Azure 存储”。 

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“使用连接字符串”、“下一步”。 

1. 选择连接的显示名称，并输入连接字符串。 然后，选择“下一步”。

1. 检查“连接摘要”，确保所有信息正确。 如果正确，请选择“连接”。 否则，请选择“后退”返回前面的页，以修复任何错误的信息。

成功添加连接后，资源树中将打开表示该连接的节点。 该资源显示在“本地和附加” > “存储帐户”下。  如果存储资源管理器无法添加你的连接，或者在成功添加连接后你无法访问自己的数据，请参阅 [Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-shared-access-signature-uri"></a>使用共享访问签名 URI

1. 选择“连接”符号打开“连接到 Azure 存储”。 

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“使用共享访问签名(SAS) URI”、“下一步”。 

1. 选择连接的显示名称，并输入共享访问签名 URI。 适用于要附加的资源类型的服务终结点应会自动填充。 如果使用的是自定义终结点，有可能不会填充该终结点。 选择“**下一步**”。

1. 检查“连接摘要”，确保所有信息正确。 如果正确，请选择“连接”。 否则，请选择“后退”返回前面的页，以修复任何错误的信息。

成功添加连接后，资源树中将打开表示该连接的节点。 该资源显示在“本地和附加” > “存储帐户” > “(附加的容器)” > “<适用于附加容器类型的服务节点>”下。   如果存储资源管理器无法添加你的连接，请参阅 [Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。 如果在成功添加连接后无法访问数据，请参阅故障排除指南。

#### <a name="use-a-name-and-key"></a>使用名称和密钥

1. 选择“连接”符号打开“连接到 Azure 存储”。 

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“使用存储帐户名和密钥”、“下一步”。 

1. 选择连接的显示名称。

1. 输入存储帐户名称及其任一访问密钥。

1. 选择要使用的**存储域**，然后选择“下一步”。

1. 检查“连接摘要”，确保所有信息正确。 如果正确，请选择“连接”。 否则，请选择“后退”返回前面的页，以修复任何错误的信息。

成功添加连接后，资源树中将打开表示该连接的节点。 该资源显示在“本地和附加” > “存储帐户”下。  如果存储资源管理器无法添加你的连接，或者在成功添加连接后你无法访问自己的数据，请参阅 [Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="attach-to-a-local-emulator"></a>附加到本地仿真器

存储资源管理器目前支持两个官方存储仿真器：

* [Azure 存储仿真器](storage/common/storage-use-emulator.md)（仅适用于 Windows）
* [Azurite](https://github.com/azure/azurite)（Windows、macOS 或 Linux）

如果仿真器正在侦听默认端口，则你可以使用“仿真器 - 默认端口”节点访问仿真器。 请留意“本地和附加” > “存储帐户”下的“仿真器 - 默认端口”。  

若要为连接使用不同的名称，或者仿真器不是在默认端口上运行，请执行以下步骤：

1. 启动仿真器。 输入命令 `AzureStorageEmulator.exe status` 显示每种服务类型的端口。

   > [!IMPORTANT]
   > 存储资源管理器不会自动启动你的仿真器。 必须手动启动它。

1. 选择“连接”符号打开“连接到 Azure 存储”。 

    ![“连接到 Azure 存储”选项][9]

1. 依次选择“附加到本地仿真器”、“下一步”。 

1. 选择连接的显示名称，并输入每种服务类型的仿真器所侦听的端口。 “附加到本地仿真器”会建议大多数仿真器的默认端口值。 “文件端口”保留空白，因为目前没有任何官方仿真器支持文件服务。 如果使用的仿真器不支持文件服务，你可以输入要使用的端口。 然后，选择“下一步”。

1. 检查“连接摘要”，确保所有信息正确。 如果正确，请选择“连接”。 否则，请选择“后退”返回前面的页，以修复任何错误的信息。

成功添加连接后，资源树中将打开表示该连接的节点。 该节点应显示在“本地和附加” > “存储帐户”下。  如果存储资源管理器无法添加你的连接，或者在成功添加连接后你无法访问自己的数据，请参阅 [Azure 存储资源管理器故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB 帐户

如果不通过 Azure 订阅管理 Azure Cosmos DB 帐户，可以使用连接字符串连接到 Azure Cosmos DB。 若要进行连接，请执行以下步骤：

1. 在“资源管理器”下，展开“本地和附加”，右键单击“Azure Cosmos DB 帐户”，然后选择“连接到 Azure Cosmos DB”。   

    ![通过连接字符串连接到 Azure Cosmos DB][21]

1. 选择“Azure Cosmos DB API”，输入“连接字符串”数据，然后选择“确定”以连接到 Azure Cosmos DB 帐户。  有关如何检索连接字符串的信息，请参阅[管理 Azure Cosmos 帐户](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![连接字符串][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>通过 URI 连接到 Azure Data Lake Store

可以访问不在订阅中的资源。 需要请求某个有权访问该资源的人员提供资源 URI。 登录后，使用该 URI 连接到 Data Lake Store。 若要进行连接，请执行以下步骤：

1. 在“资源管理器”下，展开“本地和附加”。 

1. 右键单击“Data Lake Storage Gen1”并选择“连接到 Data Lake Storage Gen1”。 

    ![“连接到 Data Lake Store”上下文菜单](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. 输入 URI，然后选择“确定”。 你的 Data Lake Store 将显示在“Data Lake Storage”下。

    ![“连接到 Data Lake Store”操作结果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

本示例使用 Data Lake Storage Gen1。 Azure Data Lake Storage Gen2 现已推出。 有关详细信息，请参阅 [什么是 Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md)。

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>在存储资源管理器中生成共享访问签名<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>帐户级共享访问签名

1. 右键单击要共享的存储帐户，并选择“获取共享访问签名”。

    ![“获取共享访问签名”上下文菜单选项][14]

1. 在“共享访问签名”中，指定要授予该帐户的时间范围和权限，然后选择“创建”。 

    ![获取共享访问签名][15]

1. 将“连接字符串”或原始“查询字符串”复制到剪贴板。 

### <a name="service-level-shared-access-signature"></a>服务级共享访问签名

可以在服务级别获取共享访问签名。 有关详细信息，请参阅[获取 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。

## <a name="search-for-storage-accounts"></a>搜索存储帐户

若要查找存储资源，可以在“资源管理器”窗格中搜索。

在搜索框中输入文本时，存储资源管理器会显示与当前已输入的搜索值匹配的所有资源。 本示例演示如何搜索**终结点**：

![存储帐户搜索][23]

> [!NOTE]
> 若要加快搜索速度，可使用“帐户管理”取消选择不包含所要搜索的项的订阅。 还可以右键单击某个节点，然后选择“从此处搜索”，从特定的节点开始搜索。
>
>

## <a name="next-steps"></a>后续步骤

* [使用存储资源管理器管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
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
