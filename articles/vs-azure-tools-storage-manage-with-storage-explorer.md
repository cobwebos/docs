<properties
	pageTitle="存储资源管理器（预览版）入门 | Azure"
	description="使用存储空间资源管理器（预览版）管理 Azure 存储空间资源"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.date="06/05/2016"
	wacn.date="" />

# 存储资源管理器（预览版）入门

## 概述 

Microsoft Azure 存储资源管理器（预览版）是一款独立应用，可用于在 Windows、OSX 和 Linux 上轻松处理 Azure 存储数据。在本文中，你将了解如何通过各种方式连接到 Azure 存储帐户并对其进行管理。

## 先决条件

- [下载并安装存储资源管理器（预览版）](http://go.microsoft.com/fwlink/?LinkId=708343)

## 连接到存储帐户或服务

存储资源管理器（预览版）提供了多种连接到存储帐户的方式。这些方式包括连接到与 Azure 订阅关联的存储帐户、连接到从其他 Azure 订阅共享的存储帐户和服务，甚至包括使用 Azure 存储模拟器连接到本地存储并对其进行管理：

- [连接到 Azure 订阅](#connect-to-an-azure-subscription) - 管理属于 Azure 订阅的存储资源。
- [连接到本地存储](#connect-to-local-storage) - 使用 Azure 存储模拟器管理本地存储。
- [附加到外部存储](#attach-or-detach-an-external-storage-account) - 使用存储帐户的帐户名称和密钥管理属于另一 Azure 订阅的存储资源。
- [使用 SAS 附加帐户](#attach-account-using-sas) - 使用 SAS 管理属于另一 Azure 订阅的存储资源。
- [使用 SAS 附加服务](#attach-service-using-sas) - 使用 SAS 管理属于另一 Azure 订阅的特定存储服务（Blob 容器、队列或表）。

## 连接到 Azure 订阅

> [AZURE.NOTE] 如果你没有 Azure 帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活你的 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

1. 启动存储资源管理器（预览版）。

1. 如果你是第一次运行存储资源管理器（预览版），或者你此前已运行过存储资源管理器（预览版），但尚未连接到 Azure 帐户，则会看到一个信息栏，你可以通过该信息栏中的信息连接到 Azure 帐户。

	![][0]
	
1. 选择“连接到 Microsoft Azure”，然后按照对话框中的说明，使用与至少一个活动 Azure 订阅相关联的 Microsoft 帐户登录。

使用 Microsoft 帐户成功登录以后，存储资源管理器（预览版）的左窗格中将会填充与所有 Azure 订阅关联的所有存储帐户，而这些 Azure 订阅又与 Microsoft 帐户关联。

### 筛选 Azure 订阅

使用存储资源管理器（预览版），你可以筛选出会在左窗格中列出其存储帐户且与已登录 Microsoft 帐户关联的 Azure 订阅。

1. 选择“设置”（齿轮）图标。

	![][1]

1. 	在左窗格顶部，你会看到一个下拉列表，其中包含你已登录的所有 Microsoft 帐户。

	![][3]
	 
1.	选择下拉列表旁边的向下箭头，以便查看所有已登录的 Microsoft 帐户以及用于添加（登录）其他 Microsoft 帐户的链接。

	![][4]

1.	从下拉列表中，选择所需 Microsoft 帐户。

1. 	左窗格将显示与所选 Microsoft 帐户关联的所有 Azure 订阅。使用每个 Azure 订阅左侧的复选框，你可以指定是否希望存储资源管理器（预览版）列出与该 Azure 订阅关联的所有存储帐户。选中/取消选中“所有订阅”即可通过切换来选择所有列出的 Azure 订阅，或者一个都不选。

	![][2]

1.	选择完要管理的 Azure 订阅以后，请选择“应用”。左窗格在更新后会针对当前 Microsoft 帐户的每个所选 Azure 订阅列出所有存储帐户。

### 添加其他 Microsoft 帐户

以下步骤演示如何通过连接到其他 Microsoft 帐户来查看每个帐户的 Azure 订阅和存储帐户。

1.	选择“设置”（齿轮）图标。

	![][1]

1. 	在左窗格顶部，你会看到一个下拉列表，其中包含当前已连接的所有 Microsoft 帐户。

	![][3]
	 
1.	选择下拉列表旁边的向下箭头，以便查看所有已登录的 Microsoft 帐户以及用于添加（登录）其他 Microsoft 帐户的链接。

	![][4]

1.	选择“添加帐户”，然后按对话框中的说明登录到与至少一个活动 Azure 订阅关联的帐户。

1.	选择与要浏览的 Azure 订阅相对应的复选框。

	![][2]

1.	选择“应用”。

### 在 Microsoft 帐户之间切换

虽然你可以连接到多个 Microsoft 帐户，但左窗格仅显示与单个（当前）Microsoft 帐户的订阅关联的存储帐户。如果连接到多个 Microsoft 帐户，你可以通过以下步骤在这些帐户之间切换：

1.	选择“设置”（齿轮）图标。

	![][1]

1. 	在左窗格顶部，你会看到一个下拉列表，其中包含当前已连接的所有 Microsoft 帐户。

	![][3]
	 
1.	选择下拉列表旁边的向下箭头，以便查看所有已登录的 Microsoft 帐户以及用于添加（登录）其他 Microsoft 帐户的链接。

	![][4]

1.	选择所需的 Microsoft 帐户。

1.	选择与要浏览的 Azure 订阅相对应的复选框。

	![][2]

1.	选择“应用”。
  
## 连接到本地存储

使用存储资源管理器（预览版），你可以通过 Azure 存储模拟器对本地存储进行操作。因此，你不需要在 Azure 上部署存储帐户就可以针对存储编写代码并对存储进行测试（因为存储帐户是通过 Azure 存储模拟器进行模拟的）。

>[AZURE.NOTE] 目前仅 Windows 支持 Azure 存储模拟器。

1. 启动存储资源管理器（预览版）。

1. 在左窗格中，展开“(开发)”节点。

	![][21]

1. 如果你尚未安装 Azure 存储模拟器，系统会提示你按照信息栏中的说明进行安装。如果该信息栏已显示，请选择“下载最新版本”，然后安装模拟器。

	![][22]

1. 安装模拟器以后，即可创建和使用本地 Blob、队列和表。若要了解如何使用每个存储帐户类型，请在下面选择相应的链接：

	- [管理 Azure Blob 存储资源](./vs-azure-tools-storage-explorer-blobs.md)
	- 管理 Azure 队列存储资源 - 即将推出
	- 管理 Azure 表存储资源 - 即将推出

## 附加或分离外部存储帐户

可以通过存储资源管理器（预览版）附加到外部存储帐户，因此可以轻松共享存储帐户。本部分介绍如何附加到外部存储帐户（以及如何从其分离）。

### 获取存储帐户凭据

若要共享外部存储帐户，首先必须由该帐户的所有者获取该帐户的凭据（帐户名称和密钥），然后将该信息共享给想要附加到该（外部）帐户的人员。可以使用以下步骤通过 Azure 门户获取存储帐户凭据：

1.	登录到 [Azure 门户](https://portal.azure.com)。
1.	选择“浏览”。
1.	选择“存储帐户”。
1.	在“存储帐户”边栏选项卡中，选择所需存储帐户。
1.	在所选存储帐户的“设置”边栏选项卡中，选择“访问密钥”。

	![][5]
	
1.	在“访问密钥”边栏选项卡中，复制“存储帐户名称”和“密钥 1”的值，以便在附加到存储帐户时使用这些值。

	![][6]

### 附加到外部存储帐户

1.	在存储资源管理器（预览版）中，右键单击“存储帐户”，然后从上下文菜单中选择“附加外部存储”。

	![][7]
	
1.	“获取存储帐户凭据”部分介绍了如何获取存储帐户名称和密钥 1 的值。这些值将在此步骤中使用。在“附加外部存储”对话框中，在“帐户名称”框中输入存储帐户名称，在“帐户密钥”框中输入密钥 1 的值。完成时选择“确定”。

	![][8]

	附加完以后，在显示外部存储帐户时，会将“(外部)”文本追加到存储帐户名称后面。

	![][9]

### 从外部存储帐户分离

1. 	右键单击要分离的外部存储帐户，然后从上下文菜单中选择“分离”。

	![][10]

1.	确认消息框出现时，选择“是”确认从外部存储帐户中分离。

	![][12]

## 使用 SAS 附加帐户

Azure 订阅管理员可以临时通过 SAS（共享访问签名）授予对存储帐户的访问权限，不需提供 Azure 订阅凭据。

为了说明这一点，假设 UserA 是 Azure 订阅的管理员，UserA 想要允许 UserB 在有限的时间内使用特定权限访问某个存储帐户：

1. UserA 生成了一个适用于特定时间段且具有所需权限的 SAS（包含存储帐户的连接字符串）。
1. UserA 将 SAS 与需要访问该存储帐户的人员（在我们的示例中为 UserB）共享。
1. UserB 通过存储资源管理器（预览版）使用所提供的 SAS 附加到属于 UserA 的帐户。

### 获取要共享的帐户的 SAS

1.	打开存储资源管理器（预览版）。
1.	在左窗格中，右键单击要共享的存储帐户，然后从上下文菜单中选择“获取共享访问签名”。

	![][13]

1. 在“共享访问签名”对话框中，指定你要授予该帐户的时间范围和权限，然后选择“创建”。

	![][14]
 
1. 此时会出现第二个“共享访问签名”对话框，其中会显示 SAS。选择“连接字符串”旁边的“复制”，将其复制到剪贴板。选择“关闭”以关闭对话框。

### 使用 SAS 附加到共享帐户

1.	打开存储资源管理器（预览版）。
1.	在左窗格中，右键单击“存储帐户”，然后从上下文菜单中，选择“使用 SAS 附加帐户”。![][15]

1. 在“使用 SAS 附加帐户”对话框中：

	- **帐户名称** - 输入要与此帐户关联的名称。**注意：**此帐户名称不需与生成 SAS 时所针对的原始存储帐户名称匹配。
 	- **连接字符串** - 粘贴先前复制的连接字符串。
 	- 完成时选择“确定”。
	
	![][16]

附加完以后，在显示存储帐户时，会将“(SAS)”文本追加到你所提供的帐户名称后面。

![][17]

## 使用 SAS 附加服务

[使用 SAS 附加帐户](#attach-account-using-sas)部分演示了 Azure 订阅管理员如何为存储帐户生成（和共享）SAS，从而授予用户对存储帐户的临时访问权限。同样可以在存储帐户中为特定服务（Blob 容器、队列或表）生成 SAS。

### 为要共享的服务生成 SAS

在这种情况下，服务可以是 Blob 容器、队列或表。以下各节说明如何生成所列服务的 SAS：

- [获取 Blob 容器的 SAS](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- 为队列获取 SAS - 即将推出
- 为表获取 SAS - 即将推出

### 使用 SAS 附加到共享帐户服务

1.	打开存储资源管理器（预览版）。
1.	在左窗格中，右键单击“存储帐户”，然后从上下文菜单中，选择“使用 SAS 附加服务”。![][18]

1. 在“使用 SAS 附加帐户”对话框中，粘贴此前复制的 SAS URI，然后选择“确定”。

	![][19]

附加完成后，新附加的服务将显示在“(服务 SAS)”节点下。

![][20]

## 搜索存储帐户

如果你的存储帐户列表很长，则可使用左窗格顶部的搜索框来快速查找特定的存储帐户。

在搜索框中键入内容时，左窗格仅显示与当前已输入搜索值匹配的存储帐户。以下屏幕快照演示了一个示例，在该示例中，我通过搜索获得了存储帐户名称中包含文本“tarcher”的所有存储帐户。

![][11]
	
若要清除搜索，请在“搜索”框中选择“x”按钮。

## 后续步骤
- [使用存储资源管理器（预览版）管理 Azure Blob 存储资源](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png

<!---HONumber=AcomDC_0718_2016-->