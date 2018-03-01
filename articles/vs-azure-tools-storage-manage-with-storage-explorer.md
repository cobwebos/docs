---
title: "存储资源管理器（预览版）入门 | Microsoft Docs"
description: "使用存储空间资源管理器（预览版）管理 Azure 存储资源"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 27b3775d81ec6dc093dae4ee46167c5d5a9c9e19
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>存储资源管理器（预览版）入门
## <a name="overview"></a>概述
Azure 存储资源管理器（预览版）是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 本文介绍如何通过多种方式连接到 Azure 存储帐户并对其进行管理。

![Microsoft Azure 存储资源管理器（预览版）][0]

## <a name="prerequisites"></a>先决条件
* [下载并安装存储资源管理器（预览版）](http://www.storageexplorer.com)

> [!NOTE]
> 对于 Ubuntu 16.04 以外的 Linux 发行版，可能需要手动安装某些依赖项。 一般情况下，以下包是必需的：
> * libgconf-2-4
> * libsecret
> * 最新 GCC
>
> 具体取决于你的发行版，可能还需要安装其他包。 存储资源管理器[发行说明](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)包含用于某些发行版的特定步骤。
>
>

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务
存储资源管理器（预览版）提供了多种连接到存储帐户的方式。 例如，可以：
* 连接到与 Azure 订阅关联的存储帐户。
* 连接到从其他 Azure 订阅共享的存储帐户和服务。
* 使用 Azure 存储模拟器连接到本地存储并对其进行管理。 

此外，可以在全球各区域 Azure 中使用存储帐户：

* [连接到 Azure 订阅](#connect-to-an-azure-subscription)：管理属于 Azure 订阅的存储资源。
* [使用本地开发存储](#work-with-local-development-storage)：使用 Azure 存储模拟器管理本地存储。
* [连接外部存储](#attach-or-detach-an-external-storage-account)：使用存储帐户的名称、密钥和终结点管理属于另一 Azure 订阅或区域 Azure 云的存储资源。
* [使用 SAS 附加存储帐户](#attach-storage-account-using-sas)：通过共享访问签名 (SAS) 管理属于另一 Azure 订阅的存储资源。
* [使用 SAS 附加服务](#attach-service-using-sas)：通过 SAS 管理属于另一 Azure 订阅的特定存储服务（Blob 容器、队列或表）。
* [使用连接字符串连接到 Azure Cosmos DB 帐户](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)：使用连接字符串管理 Cosmos DB 帐户。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅
> [!NOTE]
> 如果没有 Azure 帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。
>
>

1. 在存储资源管理器（预览版）中，选择“管理帐户”转到“帐户管理面板”。

    ![管理帐户][1]

2. 左窗格随即显示已登录的所有 Azure 帐户。 若要连接到另一个帐户，请选择“添加帐户”。

3. 若要登录到某个国家/地区云或 Azure Stack，请单击“Azure 环境”下拉列表并选择要使用的 Azure 云。 选择环境后，单击“登录...”按钮。 若要登录到 Azure Stack，请参阅[将存储资源管理器连接到 Azure Stack 订阅](azure-stack/user/azure-stack-storage-connect-se.md)了解详细信息。

    ![登录选项][2]

3. 使用 Azure 帐户成功登录后，该帐户以及与该帐户关联的 Azure Stack 订阅会添加到左窗格中。 选择使用的 Azure 订阅，然后选择“应用”（选择“所有订阅:”切换开关可选择列出的所有 Azure 订阅，或不选择其中的任何订阅）。

    ![选择 Azure 订阅][3]

    左窗格会显示与所选 Azure 订阅关联的存储帐户。

    ![选择的 Azure 订阅][4]

## <a name="work-with-local-development-storage"></a>使用本地开发存储
使用存储资源管理器（预览版），可以通过 Azure 存储模拟器对本地存储进行操作。 使用此方法可以模拟 Azure 存储的使用，而无需在 Azure 上部署存储帐户，因为 Azure 存储模拟器可模拟存储帐户。

> [!NOTE]
> 目前仅 Windows 支持 Azure 存储模拟器。
>
>

> [!NOTE]
> Azure 存储模拟器不支持文件存储。
>
>

1. 在存储资源管理器（预览版）的左窗格中，展开“(本地和附加存储)” > “存储帐户” > “(开发)” > “Blob 容器”节点。

    ![本地开发节点][5]

2. 如果尚未安装 Azure 存储模拟器，系统会提示用户按照信息栏中的说明进行安装。 如果该信息栏已显示，请选择“下载最新版本”，并安装模拟器。

    ![“下载 Azure 存储模拟器”提示][6]

3. 安装模拟器以后，即可创建和使用本地 Blob、队列和表。 若要了解如何使用每个存储帐户类型，请参阅以下指南：

    * [管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
    * 管理 Azure 文件共享存储资源：*即将推出*
    * 管理 Azure 队列存储资源：*即将推出*
    * 管理 Azure 表存储资源：*即将推出*

## <a name="attach-or-detach-an-external-storage-account"></a>附加或分离外部存储帐户
可以通过存储资源管理器（预览版）附加到外部存储帐户，因此可以轻松共享存储帐户。 本部分介绍如何附加到外部存储帐户（以及如何从其分离）。

### <a name="get-the-storage-account-credentials"></a>获取存储帐户凭据
要共享外部存储帐户，首先必须由该帐户的所有者获取该帐户的凭据（帐户名称和密钥），然后将该信息共享给想要附加到指定帐户的人员。 可以执行以下步骤，通过 Azure 门户获取存储帐户凭据：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“浏览”。

3. 选择“存储帐户”。

4. 在“存储帐户”列表中，选择所需的存储帐户。

5. 在“设置”下，选择“访问密钥”。

    ![“访问密钥”选项][7]

6. 复制“存储帐户名称”和“ 密钥 1”。

    ![访问密钥][8]

### <a name="attach-to-an-external-storage-account"></a>附加到外部存储帐户
若要附加到某个外部存储帐户，需要该帐户的名称和密钥。 “获取存储帐户凭据”部分说明了如何从 Azure 门户获取这些值。 但在门户中，帐户密钥称为 **key1**。 因此，当存储资源管理器（预览版）要求提供帐户密钥时，请输入“密钥 1”值。

1. 在存储资源管理器（预览版）中，打开“连接对话”。

    ![“连接到 Azure 存储”选项][9]

2. 在“连接对话”中，选择“使用存储帐户名称和密钥”。

    ![使用名称和密钥的选项][10]

3. 在“帐户名称”文本框中粘贴帐户名称，在“帐户密钥”文本框中粘贴帐户密钥（从 Azure 门户获取的“密钥 1”值），然后选择“下一步”。

    ![名称和密钥页][11]

    > [!NOTE]
    > 若要使用国家/地区云的名称和密钥，请使用“存储终结点域:”下拉列表选择相应的终结点域： 
    >
    >

4. 在“连接摘要”对话框中验证信息。 如果想要更改任何设置，请选择“返回”并重新输入所需的设置。 

5. 选择“连接”。

6. 成功附加存储帐户后，会显示该存储帐户，其名称后面追加了“(外部)”字样。

    ![连接到外部存储帐户的结果][12]

### <a name="detach-from-an-external-storage-account"></a>从外部存储帐户分离
1. 右键单击要分离的外部存储帐户，并选择“分离”。

    ![“从存储分离”选项][13]

2. 在确认消息中，选择“是”确认从外部存储帐户中分离。

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>使用共享访问签名 (SAS) 附加存储帐户
Azure 订阅的管理员可以通过共享访问签名 ([SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md)) 授予存储帐户的临时访问权限，不需提供 Azure 订阅凭据。

为了说明这一情形，假设 UserA 是 Azure 订阅的管理员，UserA 想要允许 UserB 在有限的时间内使用特定权限访问某个存储帐户：

1. UserA 生成了一个适用于特定时间段且具有所需权限的 SAS 连接字符串。

2. UserA 将 SAS 与需要访问该存储帐户的人员（在本示例中为 UserB）共享。  

3. UserB 通过存储资源管理器（预览版）使用所提供的 SAS 附加到属于 UserA 的帐户。

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>为要共享的帐户生成 SAS 连接字符串
1. 在存储资源管理器（预览版）中，右键单击要共享的存储帐户，并选择“获取共享访问签名...”。

    ![“获取 SAS”上下文菜单选项][14]

2. 在“生成共享访问签名”对话框中，指定要授予该帐户的时间范围和权限，然后单击“创建”按钮。

    ![“获取 SAS”对话框][15]  

3. 选择“连接字符串”文本框旁边的“复制”，将其复制到剪贴板，然后单击“关闭”。

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>使用 SAS 连接字符串附加到存储帐户
1. 在存储资源管理器（预览版）中，打开“连接对话”。

    ![“连接到 Azure 存储”选项][9]

2. 在“连接对话”对话框中，选择“使用连接字符串或共享访问签名 URI”，然后单击“下一步”。

    ![“连接到 Azure 存储”对话框][16]

3. 选择“使用连接字符串”，并在“连接字符串:”字段中粘贴连接字符串。 单击“下一步”按钮。

    ![“连接到 Azure 存储”对话框][17]

4. 在“连接摘要”对话框中验证信息。 要进行更改，请选择“上一步”，并输入所需的设置。 

5. 选择“连接”。

6. 成功附加存储帐户后，会显示该存储帐户，其名称后面追加了“(SAS)”字样。

    ![使用 SAS 附加到帐户的结果][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>使用共享访问签名 (SAS) 附加服务
“使用 SAS 附加存储帐户”部分介绍了 Azure 订阅管理员如何为存储帐户生成和共享 SAS，从而授予用户对存储帐户的临时访问权限。 同样可以在存储帐户中为特定服务（Blob 容器、队列、表或文件共享）生成 SAS。  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>为要共享的服务生成 SAS
在这种情况下，服务可以是 Blob 容器、队列、表或文件共享。 若要为列出的服务生成 SAS，请参阅：

* [获取 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* 获取文件共享的 SAS：*即将推出*
* 获取队列的 SAS：*即将推出*
* 获取表的 SAS：*即将推出*

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>使用 SAS URI 附加到共享帐户服务
1. 在存储资源管理器（预览版）中，打开“连接对话”。

    ![“连接到 Azure 存储”选项][9]

2. 在“连接对话”对话框中，选择“使用连接字符串或共享访问签名 URI”，然后单击“下一步”。

    ![“连接到 Azure 存储”对话框][16]

3. 选择“使用 SAS URI”并在“URI:”字段中粘贴 URI。 单击“下一步”按钮。

    ![“连接到 Azure 存储”对话框][19]

3. 在“连接摘要”对话框中验证信息。 要进行更改，请选择“上一步”，并输入所需的设置。 

4. 选择“连接”。

5. 成功附加服务后，该服务会显示在“(SAS 附加的服务)”节点下。

    ![使用 SAS 附加到共享服务的结果][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB 帐户
除了通过 Azure 订阅管理 Azure Cosmos DB 帐户外，连接到 Azure Cosmos DB 的另一种方法是使用连接字符串。 按照以下步骤可使用连接字符串进行连接。

1. 在左侧树中找到“本地和附加”，右键单击“Azure Cosmos DB 帐户”，然后选择“连接到 Azure Cosmos DB...”

    ![通过连接字符串连接到 Azure Cosmos DB][21]

2. 选择 Azure Cosmos DB API，粘贴“连接字符串”，然后单击“确定”连接 Azure Cosmos DB 帐户。 有关检索连接字符串的信息，请参阅[获取连接字符串](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![connection-string][22]

 ## <a name="connect-to-azure-data-lake-store-by-uri"></a>通过 URI 连接到 Azure Data Lake Store
如果需要访问某些资源，但这些资源在订阅中不存在， 而其他人授予了你相关权限，可以获取资源的 URI， 在这种情况下，可以在登录后使用该 URI 连接到 Data Lake Store。 请参阅以下步骤。
1. 打开存储资源管理器（预览版）。
2. 在左窗格中，展开“本地和附加”。
3. 右键单击“Data Lake Store”，然后从上下文菜单中选择“连接到 Data Lake Store...”

    ![“连接到 Data Lake Store”上下文菜单](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. 输入 URI，然后该工具就会导航到刚输入的 URL 的位置。

    ![“连接到 Data Lake Store”上下文对话框](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![“连接到 Data Lake Store”操作结果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>搜索存储帐户
如果需要查找某个存储资源并且不知道它的所在位置，可以使用左窗格顶部的搜索框来搜索该资源。

在搜索框中键入内容时，左窗格会显示与当前已输入搜索值匹配的所有资源。 例如，以下屏幕截图显示了“终结点”的搜索结果：

![存储帐户搜索][23]

> [!NOTE]
> 使用“帐户管理面板”取消选择不包含所要搜索的项的所有订阅，以改善搜索执行时间。 还可以右键单击某个节点，然后选择“从此处搜索”，从特定的节点开始搜索。
>
>

## <a name="next-steps"></a>后续步骤
* [使用存储资源管理器（预览版）管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
* [在 Azure 存储资源管理器（预览版）中管理 Azure Cosmos DB](./cosmos-db/storage-explorer.md)
* [使用存储资源管理器（预览版）管理 Azure Data Lake Store 资源](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
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
