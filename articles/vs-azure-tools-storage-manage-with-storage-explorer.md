---
title: 存储资源管理器入门 | Microsoft Docs
description: 利用存储资源管理器来管理 Azure 存储资源
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 2b923a381be9d6cdb1a83f582a541c9e17e117a4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086627"
---
# <a name="get-started-with-storage-explorer"></a>存储资源管理器入门

## <a name="overview"></a>概述

Microsoft Azure 存储资源管理器是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 在本文中，你将了解连接和管理 Azure 存储帐户的几种方法。

![Microsoft Azure 存储资源管理器][0]

## <a name="prerequisites"></a>先决条件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

以下 Windows 版本支持存储资源管理器：

* Windows 10（推荐）
* Windows 8
* Windows 7

对于所有版本的 Windows，都需要 .NET Framework 4.6.2 或更高版本。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

以下 macOS 版本支持存储资源管理器：

* macOS 10.12“Sierra”及更高版本

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

对于 Linux 的最常见分发， [Snap Store](https://snapcraft.io/storage-explorer)中提供存储资源管理器，这是建议的安装方法。 当新版本发布到 Snap Store 时，存储资源管理器 snap 会自动安装其所有依赖项和更新。

有关支持的分发的列表，请参阅[snapd 安装页](https://snapcraft.io/docs/installing-snapd)。

存储资源管理器要求使用密码管理器，你可能必须手动连接，然后存储资源管理器才能正常工作。 可以通过运行以下命令，将存储资源管理器连接到系统的密码管理器：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

存储资源管理器也可作为 gz 下载，但你必须手动安装依赖项。 以下 Linux 分发版支持 gz 安装：

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Gz 安装可在其他发行版上运行，但仅正式支持列出的版本。

有关在 Linux 上安装存储资源管理器的详细信息，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

---

## <a name="download-and-install"></a>下载并安装

[下载并安装存储资源管理器](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务

存储资源管理器提供了多种连接到存储帐户的方式。 一般情况下，你可以：

* [登录到 Azure 以访问自己的订阅及其资源](#sign-in-to-azure)
* [附加特定的存储或 CosmosDB 资源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登录 Azure

> [!NOTE]
> 若要在登录后完全访问资源，存储资源管理器需要管理（Azure 资源管理器）和数据层权限。 这意味着你需要 Azure Active Directory （Azure AD）权限，这将允许你访问你的存储帐户、帐户中的容器以及容器中的数据。 如果你仅在数据层具有权限，请考虑[通过 Azure AD 添加资源](#add-a-resource-via-azure-ad)。 有关存储资源管理器要求的特定权限的详细信息，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)。

1. 在存储资源管理器中，选择 "**管理帐户**" 以前往 "帐户管理" 面板。

    ![管理帐户][1]

2. 左窗格随即显示已登录的所有 Azure 帐户。 若要连接到另一个帐户，请选择 "**添加帐户**"。

3. 如果要登录到国内云或 Azure Stack，请选择 " **azure 环境**" 下拉列表，选择要使用的 azure 云。 选择环境后，选择 "**登录**" 按钮。 有关详细信息，请参阅[将存储资源管理器连接到 Azure Stack 订阅](/azure-stack/user/azure-stack-storage-connect-se)。

    ![登录选项][2]

4. 使用 Azure 帐户成功登录后，该帐户以及与该帐户关联的 Azure Stack 订阅会添加到左窗格中。 选择要使用的 Azure 订阅，并选择“应用”。 （选择 "**所有订阅**" 会在所有列出的 Azure 订阅或不在列出的任何 Azure 订阅之间切换你的选择。

    ![选择 Azure 订阅][3]

    左窗格会显示与所选 Azure 订阅关联的存储帐户。

    ![选择的 Azure 订阅][4]

### <a name="attach-a-specific-resource"></a>附加特定的资源

可以通过多种方式附加到存储资源管理器中的资源：

* [通过 Azure AD 添加资源](#add-a-resource-via-azure-ad)。 如果只有在数据层具有权限，请使用此选项添加 blob 容器或 Azure Data Lake Storage Gen2 Blob 存储容器。
* [使用连接字符串](#use-a-connection-string)。 如果有到存储帐户的连接字符串，请使用此选项。 存储资源管理器支持密钥和 [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) 连接字符串。
* [使用 SAS URI](#use-a-sas-uri)。 如果有指向 blob 容器、文件共享、队列或表的[SAS URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) ，请使用它附加到资源。 若要获取 SAS URI，可以使用[存储资源管理器](#generate-a-sas-in-storage-explorer)或 [Azure 门户](https://portal.azure.com)。
* [使用 "名称" 和 "密钥](#use-a-name-and-key)"。 如果你知道某个帐户密钥到你的存储帐户，则可以使用此选项快速连接。 存储帐户的密钥位于[Azure 门户](https://portal.azure.com)中的 "存储帐户**访问密钥**" 面板上。
* [附加到本地仿真器](#attach-to-a-local-emulator)。 如果你正在使用某个可用的 Azure 存储仿真器，则使用此选项可以轻松连接到该仿真器。
* [使用连接字符串连接到 Azure Cosmos DB 帐户](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)。 如果有到 CosmosDB 实例的连接字符串，请使用此选项。
* [按 URI 连接到 Azure Data Lake Store](#connect-to-azure-data-lake-store-by-uri)。 如果有要 Azure Data Lake Store 的 URI，请使用此选项。

#### <a name="add-a-resource-via-azure-ad"></a>通过 Azure AD 添加资源

1. 通过选择左侧垂直工具栏上的 "**连接**" 按钮，打开 "**连接**" 对话框：

    ![“连接到 Azure 存储”选项][9]

2. 如果你尚未执行此操作，请使用 "**添加 Azure 帐户**" 选项登录到有权访问该资源的 azure 帐户。 登录后，返回到 "**连接**" 对话框。

3. 选择 "**通过 Azure Active Directory （Azure AD）添加资源**"，然后选择 "**下一步**"。

4. 选择有权访问要附加到的存储资源的 Azure 帐户和租户。 选择“**下一步**”。

5. 选择要附加的资源类型，然后输入用于建立连接的信息。 在此页上输入的信息取决于要添加的资源类型。 请确保选择正确的资源类型。 输入所需信息后，选择 "**下一步**"。

6. 查看连接摘要，确保所有信息均正确。 如果是，请选择 "**连接**"。 否则，请使用 "**返回**" 按钮返回到前一页，修复任何不正确的信息。

成功添加连接后，资源树将自动转到表示连接的节点。 如果没有，请在 "**本地 & 附加** > 的**存储帐户** >  **（附加容器）**  > " "**Blob 容器**" 下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-connection-string"></a>使用连接字符串

1. 通过选择左侧垂直工具栏上的 "**连接**" 按钮，打开 "**连接**" 对话框：

    ![“连接到 Azure 存储”选项][9]

2. 选择 "**使用连接字符串**"，然后选择 "**下一步**"。

3. 为连接选择 "显示名称"，并输入连接字符串。 然后，选择“下一步”。

4. 查看连接摘要，确保所有信息均正确。 如果是，请选择 "**连接**"。 否则，请使用 "**返回**" 按钮返回到前一页，修复任何不正确的信息。

成功添加连接后，资源树将自动转到表示连接的节点。 如果没有，请在 "**本地 & 附加** > 的**存储帐户**" 下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-sas-uri"></a>使用 SAS URI

1. 通过选择左侧垂直工具栏上的 "**连接**" 按钮，打开 "**连接**" 对话框：

    ![“连接到 Azure 存储”选项][9]

2. 选择 "**使用共享访问签名（SAS） URI**"，然后选择 "**下一步**"。

3. 为连接选择一个显示名称，然后输入 SAS URI。 要附加的资源类型的服务终结点应自动填充。 如果你使用的是自定义终结点，则它可能不是。 选择“**下一步**”。

4. 查看连接摘要，确保所有信息均正确。 如果是，请选择 "**连接**"。 否则，请使用 "**返回**" 按钮返回到前一页，修复任何不正确的信息。

成功添加连接后，资源树将自动转到表示连接的节点。 如果该节点没有发送到该节点，请在 "**本地 & 附加** > 的**存储帐户** >  **（附加容器）**  > " 下，查看所*附加的容器类型的 "服务" 节点*。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="use-a-name-and-key"></a>使用名称和密钥

1. 通过选择左侧垂直工具栏上的 "**连接**" 按钮，打开 "**连接**" 对话框：

    ![“连接到 Azure 存储”选项][9]

2. 选择 "**使用存储帐户名称和密钥**"，然后选择 "**下一步**"。

3. 选择连接的显示名称。

4. 输入存储帐户名称及其访问密钥之一。

5. 选择要使用的**存储域**，然后选择 "**下一步**"。

6. 查看连接摘要，确保所有信息均正确。 如果是，请选择 "**连接**"。 否则，请使用 "**返回**" 按钮返回到前一页，修复任何不正确的信息。

成功添加连接后，资源树将自动转到表示连接的节点。 如果没有，请在 "**本地 & 附加** > 的**存储帐户**" 下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="attach-to-a-local-emulator"></a>附加到本地仿真器

存储资源管理器当前支持两个官方存储模拟器：
* [Azure 存储模拟器](storage/common/storage-use-emulator.md)（仅限 Windows）
* [Azurite](https://github.com/azure/azurite)（Windows、macOS 或 Linux）

如果模拟器正在侦听默认端口，则可以使用**仿真程序默认端口**节点（位于**本地 & 附加** > **存储帐户**下）来快速访问模拟器。

如果你想要为你的连接使用其他名称，或者，如果你的模拟器未在默认端口上运行，请执行以下步骤：

1. 启动仿真器。 当你执行此操作时，请记下模拟器针对每个服务类型侦听的端口。

   > [!IMPORTANT]
   > 存储资源管理器不会自动启动模拟器。 您必须手动启动它。

2. 通过选择左侧垂直工具栏上的 "**连接**" 按钮，打开 "**连接**" 对话框：

    ![“连接到 Azure 存储”选项][9]

3. 选择 "**附加到本地模拟器**"，然后选择 "**下一步**"。

4. 为连接选择一个 "显示名称"，并为每个服务类型输入模拟器正在侦听的端口。 文本框将从大多数仿真程序的默认端口值开始。 "**文件端口**" 框保留为空，因为这两个官方仿真器当前均不支持文件服务。 如果正在使用的模拟器支持文件，则可以输入正在使用的端口。 然后，选择“下一步”。

5. 查看连接摘要，确保所有信息均正确。 如果是，请选择 "**连接**"。 否则，请使用 "**返回**" 按钮返回到前一页，修复任何不正确的信息。

成功添加连接后，资源树将自动转到表示连接的节点。 如果没有，请在 "**本地 & 附加** > 的**存储帐户**" 下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB 帐户

作为通过 Azure 订阅管理 Azure Cosmos DB 帐户的替代方法，还可以使用连接字符串连接到 Azure Cosmos DB。 为此，请执行以下步骤：

1. 在资源树的左侧，展开 "**本地和附加**"，右键单击**Azure Cosmos DB 帐户**"，然后选择"**连接到 Azure Cosmos DB**"。

    ![通过连接字符串连接到 Azure Cosmos DB][21]

2. 选择 Azure Cosmos DB API，输入**连接字符串**数据，然后选择 **"确定"** 以连接 Azure Cosmos DB 帐户。 有关如何检索连接字符串的信息，请参阅[获取连接字符串](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![连接字符串][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>通过 URI 连接到 Azure Data Lake Store

如果要访问不在订阅中的资源，则需要有权访问该资源的人员提供资源 URI。 登录后，可以使用 URI 连接到 Data Lake Store。 为此，请执行以下步骤：

1. 打开存储资源管理器。
2. 在左窗格中，展开“本地和附加”。
3. 右键单击**Data Lake Store**。 在快捷菜单中，选择 "**连接到 Data Lake Store**"。

    ![连接到 Data Lake Store 上下文菜单](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 输入 URI。 该工具会转到刚输入的 URL 的位置。

    !["连接到 Data Lake Store 上下文" 对话框](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![连接到 Data Lake Store 结果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>在存储资源管理器中生成 SAS

### <a name="account-level-sas"></a>帐户级 SAS

1. 右键单击要共享的存储帐户，然后选择 "**获取共享访问签名**"。

    ![“获取 SAS”上下文菜单选项][14]

2. 在 "**生成共享访问签名**" 对话框中，指定要用于该帐户的时间范围和权限，然后选择 "**创建**"。

    ![“获取 SAS”对话框][15]

3. 你现在可以将**连接字符串**或原始**查询字符串**复制到剪贴板。

### <a name="service-level-sas"></a>服务级 SAS

[如何在存储资源管理器中获取 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>搜索存储帐户

如果需要查找存储资源，但不知道其所在位置，可以使用左窗格顶部的 "搜索" 框搜索资源。

在搜索框中键入内容时，左窗格会显示与当前已输入搜索值匹配的所有资源。 例如，以下屏幕截图显示了“终结点”的搜索结果：

![存储帐户搜索][23]

> [!NOTE]
> 若要加快搜索速度，请使用 "帐户管理" 面板取消选择不包含所要搜索的项的订阅。 您还可以右键单击某个节点，然后选择 "**从此处搜索**" 以从特定节点开始搜索。
>
>

## <a name="next-steps"></a>后续步骤

* [利用存储资源管理器管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
* [管理存储资源管理器中的 Azure Cosmos DB （预览）](./cosmos-db/storage-explorer.md)
* [使用存储资源管理器管理 Azure Data Lake Store 资源](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
