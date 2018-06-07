---
title: 连接到 Azure 堆栈订阅或存储帐户的存储资源管理器 |Microsoft 文档
description: 了解如何连接到 Azure 堆栈订阅的存储资源管理器
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 9704f05cc6da97e33c0043b93acedc9e66bdcc36
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714895"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>连接到 Azure 堆栈订阅或存储帐户的存储资源管理器

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在本文中，你将了解如何连接到你的 Azure 堆栈订阅和存储帐户使用存储资源管理器。 Azure 存储资源管理器是一个独立应用，可用于轻松地在 Windows、 macOS 和 Linux 上使用 Azure 堆栈存储数据。

> [!NOTE]  
> 有几种工具可用于将数据移到和从 Azure 堆栈存储。 有关详细信息，请参阅[适用于 Azure Stack 存储的数据传输工具](azure-stack-storage-transfer.md)。

如果你尚未安装存储资源管理器尚未，[下载存储资源管理器](http://www.storageexplorer.com/)并将其安装。

你连接到 Azure 堆栈订阅或存储帐户后，你可以使用[Azure 存储资源管理器文章](../../vs-azure-tools-storage-manage-with-storage-explorer.md)以便使用 Azure 堆栈数据。 

## <a name="prepare-for-connecting-to-azure-stack"></a>为连接到 Azure Stack 做准备

你需要直接访问 Azure 堆栈或存储资源管理器无法访问该 Azure 堆栈订阅的 VPN 连接。 若要了解如何设置到 Azure Stack 的 VPN 连接，请参阅[使用 VPN 连接到 Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

若是 Azure Stack 开发工具包，需要导出 Azure Stack 颁发机构根证书。

### <a name="export-and-then-import-the-azure-stack-certificate"></a>导出然后导入 Azure Stack 证书

1. 在 Azure Stack 主机或已与 Azure Stack 建立 VPN 连接的本地计算机上打开 `mmc.exe`。 

2. 在“文件”中选择“添加/删除管理单元”，并添加“证书”以管理“我的用户帐户”。

3. 在 **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** 下查找 **AzureStackSelfSignedRootCert**。

    ![通过 mmc.exe 加载 Azure Stack 根证书](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. 右键单击该证书，选择“所有任务” > “导出”，并按说明导出 **Base-64 编码 X.509 (.CER)** 证书。

    导出的证书会在下一步使用。

5. 启动存储资源管理器，并且如果你看到**连接到 Azure 存储**对话框框中，将其取消。

6. 在“编辑”菜单上，指向“SSL 证书”，然后选择“导入证书”。 通过文件选取器对话框找到并打开在上一步导出的证书。

    导入证书之后, 将提示你重新启动存储资源管理器。

    ![将证书导入存储资源管理器](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. 存储资源管理器重新启动后，选择**编辑**菜单上，并进行检查以查看是否**目标 Azure 堆栈**选择。 如果不是这样，选择**目标 Azure 堆栈**，然后重新启动存储资源管理器以使更改生效。 此配置是必需的，否则无法与 Azure Stack 环境兼容。

    ![确保选中“目标 Azure Stack”](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>连接到 Azure Stack 订阅

使用以下步骤来连接到 Azure 堆栈订阅的存储资源管理器。

1. 在存储资源管理器的左窗格中，选择**管理帐户**。 
    此时会显示你登录的所有 Microsoft 订阅。

2. 若要连接到 Azure Stack 订阅，请选择“添加帐户”。

    ![添加 Azure Stack 帐户](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. 在连接到 Azure 存储空间对话框中，在**Azure 环境**，选择**Azure**或**Azure 中国区**，取决于正在使用的 Azure 堆栈帐户，选择**登录**使用与至少一个有效的 Azure 堆栈订阅关联的 Azure 堆栈帐户进行登录。

    ![连接到 Azure 存储](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. 使用 Azure Stack 帐户成功登录后，左窗格将填充与该帐户关联的 Azure Stack 订阅。 选择要使用的 Azure Stack 订阅，并选择“应用”。 （选择或清除“所有订阅”复选框会选择所有列出的 Azure Stack 订阅，或者一个都不选。）

    ![填充“自定义云环境”对话框后，选择 Azure Stack 订阅](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    左窗格会显示与所选 Azure Stack 订阅关联的存储帐户。

    ![存储帐户列表，其中包括 Azure Stack 订阅帐户](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>连接到 Azure Stack 存储帐户

还可以使用存储帐户名称和密钥对连接到 Azure Stack 存储帐户。

1. 在存储资源管理器的左窗格中，选择管理帐户。 此时会显示你登录的所有 Microsoft 帐户。

    ![添加帐户](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. 若要连接到 Azure Stack 订阅，请选择“添加帐户”。

    ![添加帐户](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. 在“连接到 Azure 存储”对话框中，选择“使用存储帐户名称和密钥”。

4. 在“帐户名称”中输入帐户名称，将帐户密钥粘贴到“帐户密钥”文本框中，在“存储终结点域”中选择“其他(在下面输入)”，并输入 Azure Stack 终结点。

    Azure Stack 终结点包含两个部分：区域的名称和 Azure Stack 域。 在 Azure Stack 开发工具包中，默认终结点是 **local.azurestack.external**。 如果不确定你的终结点，请与云管理员联系。

    ![附加名称和密钥](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. 选择“连接”。
6. 成功附加存储帐户后，会显示该存储帐户，其名称后面追加了“(外部、其他)”字样。

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>后续步骤

* [要开始使用存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure 堆栈存储： 差异和注意事项](azure-stack-acs-differences.md)
* 若要了解有关 Azure 存储空间的详细信息，请参阅[Microsoft Azure 存储空间简介](../../storage/common/storage-introduction.md)
