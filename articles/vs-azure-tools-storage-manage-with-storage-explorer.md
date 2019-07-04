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
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508738"
---
# <a name="get-started-with-storage-explorer"></a>存储资源管理器入门

## <a name="overview"></a>概述

Azure 存储资源管理器是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 本文介绍如何通过多种方式连接到 Azure 存储帐户并对其进行管理。

![Microsoft Azure 存储资源管理器][0]

## <a name="prerequisites"></a>必备组件

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

以下版本的 Windows 支持 Azure 存储资源管理器：

* Windows 10（推荐）
* Windows 8
* Windows 7

对于所有 Windows 版本，都需要 .NET Framework 4.6.2 或更高版本。

[下载并安装存储资源管理器](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

以下版本的 macOS 支持 Azure 存储资源管理器：

* macOS 10.12“Sierra”及更高版本

[下载并安装存储资源管理器](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

以下 Linux 发行版支持 Azure 存储资源管理器：

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Azure 存储资源管理器也许可以在其他发行版上使用，但只有上面列出的是正式受支持的。

如需如何在 Linux 上安装存储资源管理器的更多帮助，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies)。

Azure 存储资源管理器[发行说明](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)包含用于某些发行版的特定步骤。

[下载并安装存储资源管理器](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务

存储资源管理器提供了多种连接到存储帐户的方式。 一般情况下您可以：

* [登录到 Azure 来访问你的订阅和它们的资源](#sign-in-to-azure)
* [附加的特定存储或 CosmosDB 资源](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>登录 Azure

> [!NOTE]
> 若要完全访问的资源，在登录后，存储资源管理器需要管理 (ARM) 和数据层权限。 这意味着你需要 Azure AD 权限可授予你访问你的存储帐户，在帐户中，容器和容器中的数据。 如果您仅可以从数据层的权限，请考虑使用[与 Azure AD 的附加](#add-a-resource-via-azure-ad)。 存储资源管理器所需的确切权限的详细信息，请参阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues)。
>
>

1. 在存储资源管理器中选择“管理帐户”，转到“帐户管理面板”。  

    ![管理帐户][1]

2. 左窗格随即显示已登录的所有 Azure 帐户。 若要连接到另一个帐户，请选择“添加帐户”  。

3. 若要登录到某个国家/地区云或 Azure Stack，请单击“Azure 环境”下拉列表并选择要使用的 Azure 云。  选择环境后，单击“登录...”按钮。  若要登录到 Azure Stack，请参阅[将存储资源管理器连接到 Azure Stack 订阅](/azure-stack/user/azure-stack-storage-connect-se)了解详细信息。

    ![登录选项][2]

4. 使用 Azure 帐户成功登录后，该帐户以及与该帐户关联的 Azure Stack 订阅会添加到左窗格中。 选择使用的 Azure 订阅，然后选择“应用”（选择“所有订阅:”切换开关可选择列出的所有 Azure 订阅，或不选择其中的任何订阅）。  

    ![选择 Azure 订阅][3]

    左窗格会显示与所选 Azure 订阅关联的存储帐户。

    ![选择的 Azure 订阅][4]

### <a name="attach-a-specific-resource"></a>附加的特定资源
    
有各种选项供附加到存储资源管理器的资源。 可以：

* [将通过 Azure AD 资源添加](#add-a-resource-via-azure-ad):如果您仅可以从数据层的权限，然后可以使用此选项以添加一个 Blob 容器或 ADLS 第 2 代 Blob 容器。
* [使用连接字符串](#use-a-connection-string):如果你有一个存储帐户的连接字符串。 存储资源管理器支持这两个密钥并[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)连接字符串。
* [使用 SAS URI](#use-a-sas-uri):如果有[SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)到 Blob 容器、 文件共享、 队列或表的 URI。 若要获取 SAS URI，您可以使用[存储资源管理器](#generate-a-sas-in-storage-explorer)或[Azure 门户](https://portal.azure.com)。
* [使用名称和密钥](#use-a-name-and-key):如果您知道帐户密钥的任何一个到你的存储帐户，可以使用此选项，来快速连接。 存储帐户的密钥都位于存储帐户**访问密钥**在边栏选项卡[Azure 门户](https://portal.azure.com)。
* [将附加到一个本地仿真程序](#attach-to-a-local-emulator):如果你使用其中一个可用的 Azure 存储仿真程序，使用此选项可以轻松地连接到的模拟器。
* [使用连接字符串连接到 Azure Cosmos DB 帐户](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)：如果你有的 CosmosDB 实例的连接字符串。
* [连接到 Azure Data Lake Store 通过 URI](#connect-to-azure-data-lake-store-by-uri):如果你具有对 Azure Data Lake Store 的 URI。

#### <a name="add-a-resource-via-azure-ad"></a>添加通过 Azure AD 资源

1. 打开**连接对话**通过单击**连接按钮**左侧垂直工具栏上。

    ![“连接到 Azure 存储”选项][9]

2. 如果尚未这样做，使用**添加 Azure 帐户**选项以在登录到有权访问资源的 Azure 帐户。 登录到返回后**连接对话框**。

3. 选择**将通过 Azure Active Directory (Azure AD) 资源添加**选项，然后单击**下一步**。

4. 选择有权访问你想要附加的存储资源和资源的订阅的 Azure 帐户，然后单击**下一步**。

5. 选择想要附加的资源类型，然后输入连接所需的信息。 此页上的输入具体取决于哪种类型的资源要添加将更改。 请确保选择正确的资源类型。 填写完所需的信息，请单击后**下一步**。

6. 查看摘要的连接，并确保所有信息正确。 如果一切正确的信息然后单击**Connect**，否则为返回到与前面的页**回**按钮以更正任何错误的信息。

连接已成功添加后，资源树将自动导航到表示连接的节点。 如果没有打开，请查看下出于某种原因**本地和附加**→**存储帐户**→ **（附加容器）** → **Blob 容器**. 如果存储资源管理器无法添加你的连接，或如果无法访问你的数据已成功添加该连接后，请查阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)有关的帮助。

#### <a name="use-a-connection-string"></a>使用连接字符串

1. 打开**连接对话**通过单击**连接按钮**左侧垂直工具栏上。

    ![“连接到 Azure 存储”选项][9]

2. 选择**使用的连接字符串**选项，然后单击**下一步**。

3. 选择你的连接的显示名称和连接字符串中输入。 然后单击“下一步”  。

4. 查看摘要的连接，并确保所有信息正确。 如果一切正确的信息然后单击**Connect**，否则为返回到与前面的页**回**按钮以更正任何错误的信息。

连接已成功添加后，资源树将自动导航到表示连接的节点。 如果没有打开，请查看下出于某种原因**本地和附加**→**存储帐户**。 如果存储资源管理器无法添加你的连接，或如果无法访问你的数据已成功添加该连接后，请查阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)有关的帮助。

#### <a name="use-a-sas-uri"></a>使用 SAS URI

1. 打开**连接对话**通过单击**连接按钮**左侧垂直工具栏上。

    ![“连接到 Azure 存储”选项][9]

2. 选择**使用共享的访问签名 (SAS) URI**选项，然后单击**下一步**。

3. 选择你的连接的显示名称并输入 SAS URI 中。 要附加的资源的类型的服务终结点应自动填充。 如果您正在使用自定义终结点，则可能它可能不会。 单击“下一步”。 

4. 查看摘要的连接，并确保所有信息正确。 如果一切正确的信息然后单击**Connect**，否则为返回到与前面的页**回**按钮以更正任何错误的信息。

连接已成功添加后，资源树将自动导航到表示连接的节点。 如果没有打开，请查看下出于某种原因**本地和附加**→**存储帐户**→ **（附加容器）** →**的类型的服务节点您附加的容器**。 如果存储资源管理器无法添加你的连接，或如果无法访问你的数据已成功添加该连接后，请查阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)有关的帮助。

#### <a name="use-a-name-and-key"></a>使用名称和密钥

1. 打开**连接对话**通过单击**连接按钮**左侧垂直工具栏上。

    ![“连接到 Azure 存储”选项][9]

2. 选择**使用存储帐户名称和密钥**选项，然后单击**下一步**。

3. 选择你的连接的显示名称。

4. 输入你的存储帐户名称和其访问密钥之一中。

5. 选择**存储域**使用，然后单击**下一步**。

4. 查看摘要的连接，并确保所有信息正确。 如果一切正确的信息然后单击**Connect**，否则为返回到与前面的页**回**按钮以更正任何错误的信息。

连接已成功添加后，资源树将自动导航到表示连接的节点。 如果没有打开，请查看下出于某种原因**本地和附加**→**存储帐户**。 如果存储资源管理器无法添加你的连接，或如果无法访问你的数据已成功添加该连接后，请查阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)有关的帮助。

#### <a name="attach-to-a-local-emulator"></a>将附加到一个本地仿真程序

存储资源管理器在所有平台上支持仿真程序。 这两个当前官方可用的仿真程序是：
* [Azure 存储模拟器](storage/common/storage-use-emulator.md)(仅 Windows)
* [Azurite](https://github.com/azure/azurite) （Windows、 macOS 或 Linux）

如果在仿真程序运行在默认端口上可以使用**仿真程序的默认端口**节点，始终可以下找到**本地和附加**→**存储帐户**若要快速访问你的仿真程序。 如果你想要使用不同的名称为你的连接，或如果在仿真程序未运行上的默认端口，然后按照以下步骤。

1. 启动模拟器。 时，请记下的哪些端口在仿真程序正在侦听的每个服务类型。 您需要知道此信息更高版本。

   > [!IMPORTANT]
   > 存储资源管理器不会自动启动模拟器。 必须自行启动。

2. 打开**连接对话**通过单击**连接按钮**左侧垂直工具栏上。

    ![“连接到 Azure 存储”选项][9]

3. 选择**附加到本地模拟器**选项，然后单击**下一步**。

4. 选择你的连接的显示名称，并在仿真程序正在侦听的每个服务类型的端口输入。 默认情况下，文本框中将包含默认端口值对于大多数仿真程序。 **文件端口**还保留为空默认情况下都不正式的仿真程序当前支持的文件服务。 如果你使用的仿真程序不但是支持，您可以输入中正在使用的端口。 单击“下一步”。 

5. 查看摘要的连接，并确保所有信息正确。 如果一切正确的信息然后单击**Connect**，否则为返回到与前面的页**回**按钮以更正任何错误的信息。

连接已成功添加后，资源树将自动导航到表示连接的节点。 如果没有打开，请查看下出于某种原因**本地和附加**→**存储帐户**。 如果存储资源管理器无法添加你的连接，或如果无法访问你的数据已成功添加该连接后，请查阅[故障排除指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)有关的帮助。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB 帐户

除了通过 Azure 订阅管理 Azure Cosmos DB 帐户外，连接到 Azure Cosmos DB 的另一种方法是使用连接字符串。 按照以下步骤可使用连接字符串进行连接。

1. 在左侧树中找到“本地和附加”  ，右键单击“Azure Cosmos DB 帐户”  ，然后选择“连接到 Azure Cosmos DB...” 

    ![通过连接字符串连接到 Azure Cosmos DB][21]

2. 选择 Azure Cosmos DB API，粘贴“连接字符串”  ，然后单击“确定”  连接 Azure Cosmos DB 帐户。 有关检索连接字符串的信息，请参阅[获取连接字符串](https://docs.microsoft.com/azure/cosmos-db/manage-account)。

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>通过 URI 连接到 Azure Data Lake Store

如果需要访问某些资源，但这些资源在订阅中不存在， 而其他人授予了你相关权限，可以获取资源的 URI， 在这种情况下，可以在登录后使用该 URI 连接到 Data Lake Store。 请参阅以下步骤。

1. 打开存储资源管理器。
2. 在左窗格中，展开“本地和附加”  。
3. 右键单击“Data Lake Store”，然后从上下文菜单中选择“连接到 Data Lake Store...”  

    ![“连接到 Data Lake Store”上下文菜单](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 输入 URI，然后该工具就会导航到刚输入的 URL 的位置。

    ![“连接到 Data Lake Store”上下文对话框](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![“连接到 Data Lake Store”操作结果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>在存储资源管理器中生成的 SAS

### <a name="account-level-sas"></a>帐户级别 SAS

1. 右键单击要共享，，然后选择存储帐户**获取共享访问签名...** .

    ![“获取 SAS”上下文菜单选项][14]

2. 在“生成共享访问签名”  对话框中，指定要授予该帐户的时间范围和权限，然后单击“创建”按钮  。

    ![“获取 SAS”对话框][15]

3. 你现在可以将**连接字符串**或原始**查询字符串**到剪贴板。

### <a name="service-level-sas"></a>服务级别 SAS

[如何获取存储资源管理器中的 blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>搜索存储帐户

如果需要查找某个存储资源并且不知道它的所在位置，可以使用左窗格顶部的搜索框来搜索该资源。

在搜索框中键入内容时，左窗格会显示与当前已输入搜索值匹配的所有资源。 例如，以下屏幕截图显示了“终结点”的搜索结果： 

![存储帐户搜索][23]

> [!NOTE]
> 使用“帐户管理面板”取消选择不包含所要搜索的项的所有订阅，以改善搜索执行时间。  还可以右键单击某个节点，然后选择“从此处搜索”，从特定的节点开始搜索。 
>
>

## <a name="next-steps"></a>后续步骤

* [使用存储资源管理器管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
* [在 Azure 存储资源管理器（预览版）中管理 Azure Cosmos DB](./cosmos-db/storage-explorer.md)
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
