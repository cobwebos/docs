---
title: 通过 SSH 连接到 SFTP 服务器
description: 使用 SSH 和 Azure 逻辑应用自动完成用于监视、创建、管理、发送和接收 SFTP 服务器文件的任务
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 02/28/2020
tags: connectors
ms.openlocfilehash: e7a0791cc2bca672e7fde142650ad25e7e8ab58b
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161868"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>使用 SSH 和 Azure 逻辑应用监视、创建和管理 SFTP 文件

若要使用[安全外壳 (SSH)](https://www.ssh.com/ssh/sftp/) 协议自动完成用于在[安全文件传输协议 (SFTP)](https://www.ssh.com/ssh/protocol/) 服务器上监视、创建、发送和接收文件的任务，可以使用 Azure 逻辑应用和 SFTP-SSH 连接器来生成并自动完成集成工作流。 SFTP 是通过任何可靠数据流提供文件访问、文件传输和文件管理的网络协议。 下面是可以自动完成的一些示例任务：

* 添加或更改文件时进行监视。
* 获取、创建、复制、重命名、更新、列出和删除文件。
* 创建文件夹。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

可以使用触发器来监视 SFTP 服务器上的事件，并使输出可用于其他操作。 可以使用操作针对 SFTP 服务器执行各种任务。 还可以让逻辑应用中的其他操作使用 SFTP 操作的输出。 例如，如果你定期从 SFTP 服务器检索文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件警报。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

有关 SFTP SSH 连接器与 SFTP 连接器之间的差异，请参阅本主题后面的[比较 sftp-SSH 与 sftp](#comparison)部分。

## <a name="limits"></a>限制

* 支持[分块](../logic-apps/logic-apps-handle-large-messages.md)的 sftp ssh 操作可处理高达 1 GB 的文件，而不支持分块的 sftp ssh 操作可处理最大为 50 MB 的文件。 尽管默认块区大小为 15 MB，但根据网络延迟、服务器响应时间等因素，此大小可以从 5 MB 开始动态更改，并逐渐增加到最大 50 MB。

  > [!NOTE]
  > 对于[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的 ise 标记版本会改用[ise 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

  块区大小与连接相关联，这意味着，可以对支持分块的操作使用相同的连接，并对不支持分块的操作使用同一个连接。 在这种情况下，不支持分块的操作的区块大小范围从 5 MB 到 50 MB。 下表显示了哪些 SFTP SSH 操作支持分块：

  | 操作 | 分块支持 |
  |--------|------------------|
  | **复制文件** | 否 |
  | **创建文件** | 是 |
  | **创建文件夹** | 不适用 |
  | **删除文件** | 不适用 |
  | **将存档提取到文件夹** | 不适用 |
  | **获取文件内容** | 是 |
  | **使用路径获取文件内容** | 是 |
  | **获取文件元数据** | 不适用 |
  | **使用路径获取文件元数据** | 不适用 |
  | **列出文件夹中的文件** | 不适用 |
  | **重命名文件** | 不适用 |
  | **更新文件** | 否 |
  |||

* SFTP SSH 触发器不支持分块。 请求文件内容时，触发器仅选择 15 MB 或更小的文件。 若要获取大于 15 MB 的文件，请改用此模式：

  * 使用返回文件属性的 SFTP SSH 触发器，如**添加或修改文件时（仅属性）** 。

  * 使用 "SFTP-SSH**获取文件内容**" 操作（读取完整的文件并隐式使用消息块）跟踪触发器。

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH 与 SFTP 的比较

下面是 SFTP-SSH 连接器与 SFTP 连接器（SFTP-SSH 连接器具有其功能）之间的其他重要差异：

* 使用[SSH.NET 库](https://github.com/sshnet/SSH.NET)，它是支持 .net 的开源安全外壳（SSH）库。

* 提供“创建文件夹”操作，用于在 SFTP 服务器上的指定路径中创建文件夹。

* 提供“重命名文件”操作，用于在 SFTP 服务器上重命名文件。

* 将 SFTP 服务器连接缓存最长 1 小时，这可以提高性能，并减少服务器的连接尝试次数。 若要设置此缓存行为的持续时间，请在 SFTP 服务器上编辑 SSH 配置中的 [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) 属性。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* SFTP 服务器地址和帐户凭据，可让逻辑应用访问 SFTP 帐户。 还需要有权访问 SSH 私钥和 SSH 私钥密码。 若要在上载大型文件时使用分块，你需要对 SFTP 服务器上的根文件夹具有读取和写入权限。 否则，会收到 "401 未授权" 错误。

  > [!IMPORTANT]
  >
  > SFTP-SSH 连接器仅支持以下私钥、格式、算法和指纹：
  >
  > * **私钥格式**： RSA （Rivest rivest-shamir-adleman rivest-shamir-adleman）和 DSA （数字签名算法）密钥，采用 OpenSSH 和 ssh.com 格式。 如果你的私钥采用 PuTTY （. .ppk）文件格式，请先[将该密钥转换为 OpenSSH （pem）文件格式](#convert-to-openssh)。
  >
  > * **加密算法**：DES-EDE3-CBC、DES-EDE3-CFB、DES-CBC、AES-128-CBC、AES-192-CBC 和 AES-256-CBC
  >
  > * **指纹**：MD5
  >
  > 向逻辑应用添加 SFTP SSH 触发器或操作后，必须提供 SFTP 服务器的连接信息。 为此连接提供 SSH 私钥时，请***不要手动输入或编辑密钥***，这可能会导致连接失败。 相反，请确保从 SSH 私钥文件***复制密钥***，并将该密钥***粘贴***到连接详细信息中。 
  > 有关详细信息，请参阅本文后面的[通过 SSH 连接到 SFTP](#connect)部分。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SFTP 帐户的逻辑应用。 若要从 SFTP-SSH 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SFTP-SSH 操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。

## <a name="how-sftp-ssh-triggers-work"></a>SFTP SSH 触发器的工作方式

SFTP SSH 触发器的工作方式是轮询 SFTP 文件系统，并查找自上次轮询以来发生更改的任何文件。 某些工具允许保留文件更改时的时间戳。 在这种情况下，必须禁用此功能才能让触发器正常工作。 下面是一些常见设置：

| SFTP 客户端 | 操作 |
|-------------|--------|
| Winscp | 转到“选项” **“首选项”** “传输” > “编辑” **“保留时间戳”** “禁用” >  >  >  >  |
| FileZilla | 转到“传输” **“保留已传输文件的时间戳”** “禁用” >  >  |
|||

当触发器找到新文件时，会检查该新文件是否完整，以及是否未部分写入。 例如，当触发器检查文件服务器时，可能正在更改某个文件。 为了避免返回部分写入的文件，该触发器会记录具有最近更改的文件的时间戳，但不会立即返回该文件。 仅当再次轮询服务器时，触发器才会返回该文件。 有时，此行为可能会导致延迟，长达触发器轮询间隔的两倍。

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>将基于 PuTTY 的密钥转换为 OpenSSH

如果你的私钥采用 PuTTY 格式，而后者使用 .ppk （PuTTY 私钥）文件扩展名，则首先将密钥转换为 OpenSSH 格式，这种格式使用 pem （隐私增强邮件）文件扩展名。

### <a name="unix-based-os"></a>基于 Unix 的操作系统

1. 如果系统上尚未安装 PuTTY 工具，请立即执行此操作，例如：

   `sudo apt-get install -y putty`

1. 运行此命令，该命令将创建可与 SFTP SSH 连接器一起使用的文件：

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   例如：

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. 如果尚未这样做，请[下载最新的 PuTTY 生成器（puttygen）工具](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)，然后启动该工具。

1. 在此屏幕上，选择 "**加载**"。

   ![选择 "加载"](./media/connectors-sftp-ssh/puttygen-load.png)

1. 以 PuTTY 格式浏览到你的私钥文件，然后选择 "**打开**"。

1. 从 "**转换**" 菜单中，选择 "**导出 OpenSSH 密钥**"。

   ![选择 "导出 OpenSSH 密钥"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. 保存带有 `.pem` 文件扩展名的私钥文件。

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>使用 SSH 连接到 SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“sftp ssh”作为筛选器。 在触发器列表下，选择所需的触发器。

   -或-

   对于现有逻辑应用，请在要添加操作的最后一个步骤下，选择“新建步骤”。 在搜索框中，输入“sftp ssh”作为筛选器。 在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 为连接提供所需的详细信息。

   > [!IMPORTANT]
   >
   > 在“SSH 私钥”属性中输入 SSH 私钥时，请遵循以下附加步骤，帮助确保提供此属性的完整正确值。 无效的密钥会导致连接失败。

   可以使用任何文本编辑器。以下步骤以 Notepad.exe 为例，说明如何正确复制并粘贴密钥。

   1. 在文本编辑器中打开 SSH 私钥文件。 这些步骤以记事本为例。

   1. 在记事本的 "**编辑**" 菜单上，选择 "全**选**"。

   1. 选择“编辑” > “复制”。

   1. 在添加的 SFTP-SSH 触发器或操作中，粘贴已复制到“SSH 私钥”属性中的完整密钥，支持换行。  请务必粘贴该密钥， 而不要手动输入或编辑密钥。

1. 输入完连接详细信息后，选择“创建”。

1. 现在，为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="examples"></a>示例

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP-SSH 触发器：添加或修改文件时

在 SFTP 服务器上添加或更改文件时，此触发器将启动逻辑应用工作流。 例如，可以添加一个条件，用于检查文件内容，并根据该内容是否符合指定的条件来获取内容。 然后可以添加一个操作，用于获取文件内容并将其放在 SFTP 服务器上的某个文件夹中。

**企业示例**：可以使用此触发器监视 SFTP 文件夹中表示客户订单的新文件。 然后，可以使用“获取文件内容”等 SFTP 操作来获取订单内容以做进一步处理，并将该订单存储在订单数据库中。

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-SSH 操作：使用路径获取内容

此操作从 SFTP 服务器上的文件中获取内容。 例如，可以在前面的示例中添加触发器，并添加文件内容必须符合的条件。 如果条件为 true，则可以运行获取内容的操作。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，如连接器的 Swagger 文件所述的触发器、操作和限制，请参阅[连接器的参考页](https://docs.microsoft.com/connectors/sftpwithssh/)。

> [!NOTE]
> 对于[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的 ise 标记版本会改用[ise 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)