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
ms.openlocfilehash: c4aad2f2f5bca25ead03518b2de9ac9315172052
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934748"
---
# <a name="get-started-with-storage-explorer"></a>存储资源管理器入门

## <a name="overview"></a>概述

Microsoft Azure 存储资源管理器是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 本文介绍如何通过多种方式连接到 Azure 存储帐户并对其进行管理。

![Microsoft Azure 存储资源管理器][0]

## <a name="prerequisites"></a>先决条件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

以下 Windows 版本支持存储资源管理器：

* Windows 10（推荐）
* Windows 8
* Windows 7

对于所有 Windows 版本，都需要 .NET Framework 4.6.2 或更高版本。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

以下 macOS 版本支持存储资源管理器：

* macOS 10.12“Sierra”及更高版本

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

对于最常见的 Linux 分发， [Snap Store](https://snapcraft.io/storage-explorer)上提供存储资源管理器，这是建议的安装方法。 当新版本发布到 Snap Store 时，存储资源管理器 snap 会自动安装其所有依赖项和更新。

有关支持的分发的列表，请访问[snapd 安装页](https://snapcraft.io/docs/installing-snapd)。

存储资源管理器要求使用密码管理器，则可能需要手动连接，然后存储资源管理器才能正常工作。 可以通过以下命令将存储资源管理器连接到系统的密码管理器：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

存储资源管理器也可作为 gz 下载，但你必须手动安装依赖项。 以下 Linux 分发版支持 gz 安装：

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Gz 安装可在其他发行版上运行，但仅正式支持上面列出的版本。

如需如何在 Linux 上安装存储资源管理器的更多帮助，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

---

## <a name="download-and-install"></a>下载并安装

[下载并安装存储资源管理器](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务

存储资源管理器提供了多种连接到存储帐户的方式。 一般情况下，你可以：

* [登录到 Azure 以访问自己的订阅及其资源](#sign-in-to-azure)
* [附加特定的存储或 CosmosDB 资源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登录 Azure

> [!NOTE]
> 登录后，存储资源管理器要求拥有管理 (ARM) 和数据层权限才能全面访问资源。 这意味着，你需要拥有 Azure AD 权限，可以访问自己的存储帐户、该帐户中的容器以及该容器中的数据。 如果你只拥有数据层的权限，请考虑[在 Azure AD 中使用附加操作](#add-a-resource-via-azure-ad)。 若要详细了解存储资源管理器所需的确切权限，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)。

1. 在存储资源管理器中选择“管理帐户”，转到“帐户管理面板”。

    ![管理帐户][1]

2. 左窗格随即显示已登录的所有 Azure 帐户。 若要连接到另一个帐户，请选择“添加帐户”。

3. 若要登录到某个国家/地区云或 Azure Stack，请单击“Azure 环境”下拉列表并选择要使用的 Azure 云。 选择环境后，单击 "**登录 ...** " 按钮。 有关详细信息，请参阅[将存储资源管理器连接到 Azure Stack 订阅](/azure-stack/user/azure-stack-storage-connect-se)。

    ![登录选项][2]

4. 使用 Azure 帐户成功登录后，该帐户以及与该帐户关联的 Azure Stack 订阅会添加到左窗格中。 选择使用的 Azure 订阅，然后选择“应用”（选择“所有订阅:”切换开关可选择列出的所有 Azure 订阅，或不选择其中的任何订阅）。

    ![选择 Azure 订阅][3]

    左窗格会显示与所选 Azure 订阅关联的存储帐户。

    ![选择的 Azure 订阅][4]

### <a name="attach-a-specific-resource"></a>附加特定的资源

可以使用不同的选项在存储资源管理器中附加到资源：

* [通过 Azure AD 添加资源](#add-a-resource-via-azure-ad)：如果你只拥有数据层的权限，可以使用此选项来添加 Blob 容器或 ADLS Gen2 Blob 容器。
* [使用连接字符串](#use-a-connection-string)：如果已获取存储帐户的连接字符串。 存储资源管理器支持密钥和 [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) 连接字符串。
* [使用 SAS URI](#use-a-sas-uri)：如果已获取 Blob 容器、文件共享、队列或表的 [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI。 若要获取 SAS URI，可以使用[存储资源管理器](#generate-a-sas-in-storage-explorer)或 [Azure 门户](https://portal.azure.com)。
* [使用名称和密钥](#use-a-name-and-key)：如果你知道存储帐户的任一帐户密钥，则可以使用此选项快速建立连接。 存储帐户的密钥位于[Azure 门户](https://portal.azure.com)上的 "存储帐户**访问密钥**" 面板中。
* [附加到本地仿真器](#attach-to-a-local-emulator)：如果你正在使用某个可用的 Azure 存储仿真器，则使用此选项可以轻松连接到该仿真器。
* [使用连接字符串连接到 Azure Cosmos DB 帐户](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)：如果已获取 CosmosDB 实例的连接字符串。
* [通过 URI 连接到 Azure Data Lake Store](#connect-to-azure-data-lake-store-by-uri)：如果已获取 Azure Data Lake Store 的 URI。

#### <a name="add-a-resource-via-azure-ad"></a>通过 Azure AD 添加资源

1. 单击左侧垂直工具栏上的连接按钮打开“连接”对话框。

    ![“连接到 Azure 存储”选项][9]

2. 如果你尚未执行此操作，请使用 "**添加 Azure 帐户**" 选项登录到有权访问该资源的 azure 帐户。 登录后，返回到“连接”对话框。

3. 选择“通过 Azure Active Directory (Azure AD)添加资源”选项，然后单击“下一步”。

4. 选择有权访问要附加到的存储资源的 Azure 帐户和租户。 单击“下一步”。

5. 选择要附加的资源类型，然后输入用于建立连接的信息。 此页上的输入将根据要添加的资源类型而发生更改。 请确保选择正确的资源类型。 填写所需信息后，单击 "**下一步**"。

6. 检查连接摘要，确保所有信息正确。 如果所有信息都正确无误，请单击 "**连接**"。 否则，请返回到上一页，其中包含 "**后退**" 按钮以更正任何不正确的信息。

成功添加连接后，资源树将自动导航到表示连接的节点。 如果出于某种原因，你还可以在 "**本地 & 附加**的→**存储帐户**→ **（附加容器）** → **Blob 容器**" 下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以获取帮助。

#### <a name="use-a-connection-string"></a>使用连接字符串

1. 单击左侧垂直工具栏上的连接按钮打开“连接”对话框。

    ![“连接到 Azure 存储”选项][9]

2. 选择“使用连接字符串”选项并单击“下一步”。

3. 选择连接的显示名称，并输入连接字符串。 然后单击“下一步”。

4. 检查连接摘要，确保所有信息正确。 如果所有信息都正确无误，请单击 "**连接**"，否则请返回到前一页，其中包含 "**后退**" 按钮以更正任何不正确的信息。

成功添加连接后，资源树将自动导航到表示连接的节点。 如果出于某种原因，你还可以在 "**本地 & 附加**" →**存储帐户**下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以获取帮助。

#### <a name="use-a-sas-uri"></a>使用 SAS URI

1. 单击左侧垂直工具栏上的连接按钮打开“连接”对话框。

    ![“连接到 Azure 存储”选项][9]

2. 选择“使用共享访问签名(SAS) URI”选项并单击“下一步”。

3. 选择连接的显示名称，并输入 SAS URI。 要附加的资源类型的服务终结点应自动填充。 如果使用的是自定义终结点，则可能不会。 单击“下一步”。

4. 检查连接摘要，确保所有信息正确。 如果所有信息都正确无误，请单击 "**连接**"，否则请返回到前一页，其中包含 "**后退**" 按钮以更正任何错误的信息。

成功添加连接后，资源树将自动导航到表示连接的节点。 你还可以在 "**本地 &** " "连接的" → "**存储帐户**" → **（附加容器）** →**你附加的容器类型的服务节点**（如果出于某种原因而不是）。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以获取帮助。

#### <a name="use-a-name-and-key"></a>使用名称和密钥

1. 单击左侧垂直工具栏上的连接按钮打开“连接”对话框。

    ![“连接到 Azure 存储”选项][9]

2. 选择“使用存储帐户名称和密钥”选项并单击“下一步”。

3. 选择连接的显示名称。

4. 输入存储帐户名称及其任一访问密钥。

5. 选择要使用的**存储域**并单击“下一步”。

4. 检查连接摘要，确保所有信息正确。 如果所有信息都正确无误，请单击 "**连接**"，否则请返回到前一页，其中包含 "**后退**" 按钮以更正任何不正确的信息。

成功添加连接后，资源树将自动导航到表示连接的节点。 如果出于某种原因，你还可以在 "**本地 & 附加**" →**存储帐户**下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以获取帮助。

#### <a name="attach-to-a-local-emulator"></a>附加到本地仿真器

存储资源管理器当前支持两个官方存储模拟器：
* [Azure 存储仿真器](storage/common/storage-use-emulator.md)（仅适用于 Windows）
* [Azurite](https://github.com/azure/azurite)（Windows、macOS 或 Linux）

如果模拟器正在侦听默认端口，则可以使用**仿真程序默认端口**节点（位于 "**本地 &** " → "**存储帐户**" 下）来快速访问模拟器。

如果你想要为你的连接使用其他名称，或者，如果你的模拟器未在默认端口上运行：

1. 启动仿真器。 执行此操作时，请记下每种服务类型的仿真器所侦听的端口。

   > [!IMPORTANT]
   > 存储资源管理器不会自动启动模拟器。 必须自行启动。

2. 单击左侧垂直工具栏上的连接按钮打开“连接”对话框。

    ![“连接到 Azure 存储”选项][9]

3. 选择“附加到本地仿真器”选项并单击“下一步”。

4. 选择连接的显示名称，并输入每种服务类型的仿真器所侦听的端口。 文本框将从大多数仿真程序的默认端口值开始。 **文件端口**保留空白，因为这两个官方仿真器当前均不支持文件服务。 如果正在使用的模拟器支持文件，则可以输入正在使用的端口。 单击“下一步”。

5. 检查连接摘要，确保所有信息正确。 如果所有信息都正确无误，请单击 "**连接**"，否则请返回到上一页，其中包含 "**后退**" 按钮以更正任何错误的信息。

成功添加连接后，资源树将自动导航到表示连接的节点。 如果出于某种原因，你还可以在 "**本地 & 附加**" →**存储帐户**下查看。 如果存储资源管理器无法添加你的连接，或者在成功添加连接后无法访问你的数据，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)以获取帮助。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB 帐户

除了通过 Azure 订阅管理 Azure Cosmos DB 帐户外，连接到 Azure Cosmos DB 的另一种方法是使用连接字符串。 按照以下步骤可使用连接字符串进行连接。

1. 在左侧树中找到“本地和附加”，右键单击“Azure Cosmos DB 帐户”，然后选择“连接到 Azure Cosmos DB...”

    ![通过连接字符串连接到 Azure Cosmos DB][21]

2. 选择 Azure Cosmos DB API，粘贴“连接字符串”，然后单击“确定”连接 Azure Cosmos DB 帐户。 有关检索连接字符串的信息，请参阅[获取连接字符串](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>通过 URI 连接到 Azure Data Lake Store

如果需要访问不在订阅中的资源，则需要具有访问权限的人员提供资源 URI。 登录后，可以按照以下步骤使用 URI 连接到 Data Lake Store：

1. 打开存储资源管理器。
2. 在左窗格中，展开“本地和附加”。
3. 右键单击“Data Lake Store”，然后从上下文菜单中选择“连接到 Data Lake Store...”

    ![“连接到 Data Lake Store”上下文菜单](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 输入 URI，然后该工具就会导航到刚输入的 URL 的位置。

    ![“连接到 Data Lake Store”上下文对话框](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![“连接到 Data Lake Store”操作结果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>在存储资源管理器中生成 SAS

### <a name="account-level-sas"></a>帐户级 SAS

1. 右键单击要共享的存储帐户，并选择“获取共享访问签名...”。

    ![“获取 SAS”上下文菜单选项][14]

2. 在 "**生成共享访问签名**" 对话框中，指定要用于帐户的时间范围和权限。 单击“创建”。

    ![“获取 SAS”对话框][15]

3. 现在可将“连接字符串”或原始“查询字符串”复制到剪贴板。

### <a name="service-level-sas"></a>服务级 SAS

[如何在存储资源管理器中获取 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>搜索存储帐户

如果需要查找存储资源，但不知道其所在位置，可以使用左窗格顶部的 "搜索" 框搜索资源。

在搜索框中键入内容时，左窗格会显示与当前已输入搜索值匹配的所有资源。 例如，以下屏幕截图显示了“终结点”的搜索结果：

![存储帐户搜索][23]

> [!NOTE]
> 使用“帐户管理面板”取消选择不包含所要搜索的项的所有订阅，以改善搜索执行时间。 还可以右键单击某个节点，然后选择“从此处搜索”，从特定的节点开始搜索。
>
>

## <a name="next-steps"></a>后续步骤

* [使用存储资源管理器管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
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
