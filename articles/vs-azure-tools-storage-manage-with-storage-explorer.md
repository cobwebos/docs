---
title: "存储资源管理器（预览版）入门 | Microsoft Docs"
description: "使用存储空间资源管理器（预览版）管理 Azure 存储空间资源"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 07b62cd6f6deb0cf3ff1c806204ebc26c773a164
ms.lasthandoff: 04/13/2017


---
# <a name="getting-started-with-storage-explorer-preview"></a>存储资源管理器（预览版）入门
## <a name="overview"></a>概述
Microsoft Azure 存储资源管理器（预览版）是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。 本文介绍如何通过各种方式连接到 Azure 存储帐户并对其进行管理。

![Microsoft Azure 存储资源管理器（预览版）][15]

## <a name="prerequisites"></a>先决条件
* [下载并安装存储资源管理器（预览版）](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务
存储资源管理器（预览版）提供了多种连接到存储帐户的方式。 这些方式包括连接到与 Azure 订阅关联的存储帐户、连接到从其他 Azure 订阅共享的存储帐户和服务，甚至包括使用 Azure 存储模拟器连接到本地存储并对其进行管理。 此外，可以在全球各区域 Azure 中使用存储帐户：

* [连接到 Azure 订阅](#connect-to-an-azure-subscription) - 管理属于 Azure 订阅的存储资源。
* [使用本地开发存储](#work-with-local-development-storage) - 使用 Azure 存储模拟器管理本地存储。
* [连接外部存储](#attach-or-detach-an-external-storage-account) - 使用存储帐户的名称、密钥和终结点管理属于另一 Azure 订阅或区域 Azure 云的存储资源。
* [使用 SAS 附加存储帐户](#attach-storage-account-using-sas) - 使用 SAS 管理属于另一 Azure 订阅的存储资源。
* [使用 SAS 附加服务](#attach-service-using-sas) - 使用 SAS 管理属于另一 Azure 订阅的特定存储服务（Blob 容器、队列或表）。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅
> [!NOTE]
> 如果没有 Azure 帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。
>
>

1. 在存储资源管理器（预览版）中，选择“Azure 帐户设置”。

    ![Azure 帐户设置][0]
2. 左窗格显示已登录的所有 Microsoft 帐户。 若要连接到另一个帐户，请选择“添加帐户”，然后按对话框中的说明，使用至少与一个活动 Azure 订阅相关联的 Microsoft 帐户登录。
> [!NOTE]
>当前不支持通过登录连接区域 Azure（如 Black Forest Azure、Fairfax Azure 和 Mooncake Azure）。 请参阅**连接或分离外部存储帐户**部分，了解如何连接区域 Azure 存储帐户。

3. 使用 Microsoft 帐户成功登录后，左窗格将填充与该帐户关联的 Azure 订阅。 选择要使用的 Azure 订阅，然后选择“应用”。 （选择“所有订阅”会选择所有列出的 Azure 订阅，或者一个都不选。）

    ![选择 Azure 订阅][3]
4. 左窗格会显示与所选 Azure 订阅关联的存储帐户。

    ![选择的 Azure 订阅][4]

## <a name="connect-to-an-azure-stack-subscription"></a>连接到 Azure Stack 订阅

1. 需要先建立 VPN 连接，然后存储资源管理器才能对 Azure Stack 订阅进行远程访问。 若要了解如何设置到 Azure Stack 的 VPN 连接，请参阅[使用 VPN 连接到 Azure Stack](azure-stack/azure-stack-connect-azure-stack.md#connect-with-vpn)

2. 对于 Azure Stack POC，需导出 Azure Stack 证书颁发机构的根证书。 在 MAS-CON01 上打开 `mmc.exe`，前者是 Azure Stack 主机或本地机，具有到 Azure Stack 的 VPN 连接。 在“文件”中选择“添加/删除管理单元”，然后添加“证书”以管理“本地计算机”的“计算机帐户”。

   ![通过 mmc.exe 加载 Azure stack 根证书][25]   

   在 **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** 下查找 **AzureStackCertificationAuthority**。 右键单击该项目，选择“所有任务”->“导出”。 然后按照对话框中的说明，导出类型为“Base-64 编码 X.509 (.CER)”的证书。 导出的证书将在下一步使用。   

   ![导出 Azure Stack 根证书颁发机构的根证书][26]   

3. 在存储资源管理器（预览版）中，依次选择“编辑”菜单、“SSL 证书”和“导入证书”。 通过文件选取器对话框找到并打开在上一步浏览过的证书。 导入后，系统会提示你重新启动存储资源管理器。

   ![将证书导入存储资源管理器（预览版）][27]

4. 存储资源管理器（预览版）重新启动以后，即可选择“编辑”菜单，确保选中“目标 Azure Stack”。 如果尚未选中，请将其选中，然后重新启动存储资源管理器，使更改生效。 此配置是必需的，否则无法与 Azure Stack 环境兼容。

   ![确保选中“目标 Azure Stack”][28]

5. 在左侧栏中，选择“管理帐户”。 左窗格显示你已登录的所有 Microsoft 帐户。 若要连接到 Azure Stack 帐户，请选择“添加帐户”。

   ![添加 Azure Stack 帐户][29]

6. 在“添加新帐户”对话框的“Azure 环境”下选择“创建自定义环境”，然后单击“下一步”。

7. 输入 Azure Stack 自定义环境的所有必填信息，然后单击“登录”。  在“登录到自定义云环境”对话框中填充信息，以便使用 Azure Stack 帐户登录，该帐户与至少一个活动的 Azure Stack 订阅相关联。 对话框中每个字段的详细信息如下所示：

    * **环境名称** - 用户可以自定义此字段。
    * **证书颁发机构** - 此值应为 https://login.windows.net。 对于 Azure 中国区 (Mooncake)，请使用 https://login.chinacloudapi.cn。
    * **登录资源 ID** - 执行以下 PowerShell 即可检索此值：

    如果你是云管理员：

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://adminmanagement.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    如果你是租户：

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://management.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    * **Graph 终结点** - 此值应为 https://graph.windows.net。 对于 Azure 中国区 (Mooncake)，请使用 https://graph.chinacloudapi.cn。
    * **ARM 资源 ID** - 使用与登录资源 ID 相同的值。
    * **ARM 资源终结点** - ARM 资源终结点的示例：

    云管理员：https://adminmanagement.local.azurestack.external   
    租户：https://management.local.azurestack.external
 
    * **租户 ID** - 可选。 只有在必须指定目录的情况下，才提供此值。

8. 使用 Azure Stack 帐户成功登录后，左窗格将填充与该帐户关联的 Azure Stack 订阅。 选择要使用的 Azure Stack 订阅，然后选择“应用”。 （选择“所有订阅”会选择所有列出的 Azure Stack 订阅，或者一个都不选。）

   ![填充“自定义云环境”对话框后，选择 Azure Stack 订阅][30]

9. 左窗格会显示与所选 Azure Stack 订阅关联的存储帐户。

   ![存储帐户列表，其中包括 Azure Stack 订阅帐户][31]

## <a name="work-with-local-development-storage"></a>使用本地开发存储
使用存储资源管理器（预览版），你可以通过 Azure 存储模拟器对本地存储进行操作。 因此，你不需要在 Azure 上部署存储帐户就可以针对存储编写代码并对存储进行测试（因为存储帐户是通过 Azure 存储模拟器进行模拟的）。

> [!NOTE]
> 目前仅 Windows 支持 Azure 存储模拟器。
>
>

1. 在存储资源管理器（预览版）的左窗格中，展开“(本地和附加存储)” > “存储帐户” > “(开发)”节点。

    ![本地开发节点][21]
2. 如果尚未安装 Azure 存储模拟器，系统会提示用户按照信息栏中的说明进行安装。 如果该信息栏已显示，请选择“下载最新版本”，然后安装模拟器。

    ![“下载 Azure 存储模拟器”提示][22]
3. 安装模拟器以后，即可创建和使用本地 Blob、队列和表。 若要了解如何使用每个存储帐户类型，请选择下面的一个链接：

   * [管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
   * 管理 Azure 文件共享存储资源 - *即将推出*
   * 管理 Azure 队列存储资源 - *即将推出*
   * 管理 Azure 表存储资源 - *即将推出*

## <a name="attach-or-detach-an-external-storage-account"></a>附加或分离外部存储帐户
可以通过存储资源管理器（预览版）附加到外部存储帐户，因此可以轻松共享存储帐户。 本部分介绍如何附加到外部存储帐户（以及如何从其分离）。

### <a name="get-the-storage-account-credentials"></a>获取存储帐户凭据
若要共享外部存储帐户，首先必须由该帐户的所有者获取该帐户的凭据（帐户名称和密钥），然后将该信息共享给想要附加到该（外部）帐户的人员。 可以使用以下步骤通过 Azure 门户获取存储帐户凭据：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“浏览”。
3. 选择“存储帐户”。
4. 在“存储帐户”边栏选项卡中，选择所需的存储帐户。
5. 在所选存储帐户的“设置”边栏选项卡中，选择“访问密钥”。

   ![“访问密钥”选项][5]
6. 在“访问密钥”边栏选项卡中，复制“存储帐户名称”和“密钥 1”的值，以便在附加到存储帐户时使用这些值。

   ![访问密钥][6]

### <a name="attach-to-an-external-storage-account"></a>附加到外部存储帐户
若要附加到某个外部存储帐户，需要该帐户的名称和密钥。 “获取存储帐户凭据”部分说明了如何从 Azure 门户获取这些值。 但请注意，在门户中，帐户密钥称为“密钥 1”，因此，当存储资源管理器（预览版）要求输入帐户密钥时，需要输入（或粘贴）“密钥 1”的值。

1. 在存储资源管理器（预览版）中，选择“连接到 Azure 存储”。

   ![“连接到 Azure 存储”选项][23]
2. 在“连接到 Azure 存储”对话框中，指定帐户密钥（Azure 门户中的“密钥 1”值），然后选择“下一步”。
> [!NOTE]
> 在区域 Azure 上，可以从存储帐户输入存储连接字符串。 例如，输入类似于以下内容的连接字符串以连接 Azure Black Forest 存储帐户：DefaultEndpointsProtocol = https;AccountName = cawatest03;AccountKey =<storage_account_key>;EndpointSuffix=core.cloudapi.de；可按照**获取存储帐户凭据**部分中所述的方式从 Azure 门户获取连接字符串

   ![“连接到 Azure 存储”对话框][24]

3. 在“附加外部存储”对话框中的“帐户名”框内输入存储帐户名，指定其他任何所需的设置，完成后选择“下一步”。

   ![“附加外部存储”对话框][8]
4. 在“连接摘要”对话框中检查信息。 如果想要更改任何设置，请选择“返回”并重新输入所需的设置。 完成后，选择“连接”。
5. 建立连接后，将显示外部存储帐户，其中在存储帐户名的后面追加了“(外部)”字样。

   ![连接到外部存储帐户的结果][9]

### <a name="detach-from-an-external-storage-account"></a>从外部存储帐户分离
1. 右键单击要分离的外部存储帐户，然后从上下文菜单中选择“分离”。

   ![“从存储分离”选项][10]
2. 确认消息框出现时，选择“是”确认从外部存储帐户中分离。

## <a name="attach-storage-account-using-sas"></a>使用 SAS 附加存储帐户
Azure 订阅管理员可以临时通过 [SAS（共享访问签名）](storage/storage-dotnet-shared-access-signature-part-1.md) 授予对存储帐户的访问权限，不需提供 Azure 订阅凭据。

为了说明这一点，假设 UserA 是 Azure 订阅的管理员，UserA 想要允许 UserB 在有限的时间内使用特定权限访问某个存储帐户：

1. UserA 生成了一个适用于特定时间段且具有所需权限的 SAS（包含存储帐户的连接字符串）。
2. UserA 将 SAS 与需要访问该存储帐户的人员（在我们的示例中为 UserB）共享。  
3. UserB 通过存储资源管理器（预览版）使用所提供的 SAS 附加到属于 UserA 的帐户。

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>获取要共享的帐户的 SAS
1. 在存储资源管理器（预览版）中，右键单击要共享的存储帐户，然后从上下文菜单中选择“获取共享访问签名”。

   ![“获取 SAS”上下文菜单选项][13]
2. 在“共享访问签名”对话框中，指定要授予该帐户的时间范围和权限，然后选择“创建”。

    ![“获取 SAS”对话框][14]
3. 第二个“共享访问签名”对话框会显示 SAS。 选择“连接字符串”旁边的“复制”，将其复制到剪贴板。 选择“关闭”以关闭对话框。

### <a name="attach-to-the-shared-account-using-the-sas"></a>使用 SAS 附加到共享帐户
1. 在存储资源管理器（预览版）中，选择“连接到 Azure 存储”。

   ![“连接到 Azure 存储”选项][23]
2. 在“连接到 Azure 存储”对话框中指定连接字符串，然后选择“下一步”。

   ![“连接到 Azure 存储”对话框][24]
3. 在“连接摘要”对话框中检查信息。 如果想要更改任何设置，请选择“返回”并重新输入所需的设置。 完成后，选择“连接”。
4. 附加完以后，在显示存储帐户时，会将“(SAS)”文本追加到所提供的帐户名称后面。

   ![使用 SAS 附加到帐户的结果][17]

## <a name="attach-service-using-sas"></a>使用 SAS 附加服务
[使用 SAS 附加存储帐户](#attach-storage-account-using-sas) 部分演示了 Azure 订阅管理员如何为存储帐户生成（和共享）SAS，从而授予用户对存储帐户的临时访问权限。 同样可以在存储帐户中为特定服务（Blob 容器、队列或表）生成 SAS。  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>为要共享的服务生成 SAS
在这种情况下，服务可以是 Blob 容器、队列或表。 以下各节说明如何生成所列服务的 SAS：

* [获取 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* 获取文件共享的 SAS - *即将推出*
* 获取队列的 SAS - *即将推出*
* 获取表的 SAS - *即将推出*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>使用 SAS 附加到共享帐户服务
1. 在存储资源管理器（预览版）中，选择“连接到 Azure 存储”。

   ![“连接到 Azure 存储”选项][23]
2. 在“连接到 Azure 存储”对话框中指定 SAS URI，然后选择“下一步”。

   ![“连接到 Azure 存储”对话框][24]
3. 在“连接摘要”对话框中检查信息。 如果想要更改任何设置，请选择“返回”并重新输入所需的设置。 完成后，选择“连接”。
4. 附加完成后，新附加的服务显示在“(服务 SAS)”节点下。

   ![使用 SAS 附加到共享服务的结果][20]

## <a name="search-for-storage-accounts"></a>搜索存储帐户
如果你的存储帐户列表很长，则可使用左窗格顶部的搜索框来快速查找特定的存储帐户。

在搜索框中键入内容时，左窗格仅显示与当前已输入搜索值匹配的存储帐户。 以下屏幕快照演示了一个示例，在该示例中，我通过搜索获得了存储帐户名称中包含文本“tarcher”的所有存储帐户。

![存储帐户搜索][11]

若要清除搜索，请在“搜索”框中选择“x”按钮。

## <a name="next-steps"></a>后续步骤
* [使用存储资源管理器（预览版）管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png

