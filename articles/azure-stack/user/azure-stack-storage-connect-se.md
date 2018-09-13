---
title: 将存储资源管理器连接到 Azure Stack 订阅或存储帐户 | Microsoft Docs
description: 了解如何将存储资源管理器连接到 Azure Stack 订阅
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/15/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2f974b7773e7a4cbc0eda32a267bb5ab939644d8
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2018
ms.locfileid: "35998412"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>将存储资源管理器连接到 Azure Stack 订阅或存储帐户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍如何使用存储资源管理器连接到 Azure Stack 订阅和存储帐户。 Azure 存储资源管理器是一款独立应用，可用于在 Windows、macOS 和 Linux 上轻松处理 Azure Stack 存储数据。

> [!NOTE]  
> 有几个工具可用于将数据移进/移出 Azure Stack 存储。 有关详细信息，请参阅[适用于 Azure Stack 存储的数据传输工具](azure-stack-storage-transfer.md)。

如果尚未安装存储资源管理器，请[下载存储资源管理器](http://www.storageexplorer.com/)并安装它。

连接到 Azure Stack 订阅或存储帐户后，可以使用 [Azure 存储资源管理器文章](../../vs-azure-tools-storage-manage-with-storage-explorer.md)处理 Azure Stack 数据。 

## <a name="prepare-for-connecting-to-azure-stack"></a>为连接到 Azure Stack 做准备

需要可以直接访问 Azure Stack 或 VPN 连接，存储资源管理器才能访问 Azure Stack 订阅。 若要了解如何设置到 Azure Stack 的 VPN 连接，请参阅[使用 VPN 连接到 Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

若是 Azure Stack 开发工具包，需要导出 Azure Stack 颁发机构根证书。

### <a name="export-and-then-import-the-azure-stack-certificate"></a>导出然后导入 Azure Stack 证书

1. 在 Azure Stack 主机或已与 Azure Stack 建立 VPN 连接的本地计算机上打开 `mmc.exe`。 

2. 在“文件”中选择“添加/删除管理单元”，并添加“证书”以管理“我的用户帐户”。

3. 在 **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** 下查找 **AzureStackSelfSignedRootCert**。

    ![通过 mmc.exe 加载 Azure Stack 根证书](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. 右键单击该证书，选择“所有任务” > “导出”，并按说明导出 **Base-64 编码 X.509 (.CER)** 证书。

    导出的证书会在下一步使用。

5. 启动存储资源管理器，如果看到“连接到 Azure 存储”对话框，请将其取消。

6. 在“编辑”菜单上，指向“SSL 证书”，然后选择“导入证书”。 通过文件选取器对话框找到并打开在上一步导出的证书。

    导入证书后，系统会提示重启存储资源管理器。

    ![将证书导入到存储资源管理器](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. 在存储资源管理器重新启动后，选择“编辑”菜单，并检查是否选择了“目标 Azure Stack”。 如果没有，请选择“目标 Azure Stack”，然后重启存储资源管理器，使更改生效。 此配置是必需的，否则无法与 Azure Stack 环境兼容。

    ![确保选中“目标 Azure Stack”](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>使用 Azure AD 连接到 Azure Stack 订阅

使用以下步骤将存储资源管理器连接到属于 Azure Active Directory (Azure AD) 帐户的 Azure Stack 订阅。

1. 在存储资源管理器的左窗格中，选择“管理帐户”。 
    此时会显示你登录的所有 Microsoft 订阅。

2. 若要连接到 Azure Stack 订阅，请选择“添加帐户”。

    ![添加 Azure Stack 帐户](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. 在“连接到 Azure 存储”对话框中的“Azure 环境”下，选择“Azure”或“Azure 中国”（取决于所用的 Azure Stack 帐户），然后选择“登录”，以登录到至少与一个活动 Azure Stack 订阅关联的 Azure Stack 帐户。

    ![连接到 Azure 存储](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. 使用 Azure Stack 帐户成功登录后，左窗格将填充与该帐户关联的 Azure Stack 订阅。 选择要使用的 Azure Stack 订阅，并选择“应用”。 （选择或清除“所有订阅”复选框会选择所有列出的 Azure Stack 订阅，或者一个都不选。）

    ![填充“自定义云环境”对话框后，选择 Azure Stack 订阅](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    左窗格会显示与所选 Azure Stack 订阅关联的存储帐户。

    ![存储帐户列表，其中包括 Azure Stack 订阅帐户](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>使用 AD FS 帐户连接到 Azure Stack 订阅

> [!Note]  
> Azure 联合身份验证服务 (AD FS) 登录体验支持存储资源管理器 1.2.0，或装有 Azure Stack 1804 或更高更新版的更高存储资源管理器版本。
使用以下步骤将存储资源管理器连接到属于 AD FS 帐户的 Azure Stack 订阅。

1. 选择“管理帐户”。 资源管理器会列出你登录到的 Microsoft 订阅。
2. 选择“添加帐户”连接到 Azure Stack 订阅。

    ![添加帐户](media/azure-stack-storage-connect-se/add-an-account.png)

3. 选择“**下一步**”。 在“连接到 Azure 存储”对话框中的“Azure 环境”下，选择“使用自定义环境”，然后单击“下一步”。

    ![连接到 Azure 存储](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. 输入 Azure Stack 自定义环境的所需信息。 

    | 字段 | 说明 |
    | ---   | ---   |
    | 环境名称 | 用户可以自定义此字段。 |
    | Azure 资源管理器终结点 | Azure Stack 开发工具包的 Azure 资源管理器资源终结点示例。<br>对于运营商： https://adminmanagement.local.azurestack.external <br> 对于用户： https://management.local.azurestack.external |

    如果使用 Azure Stack 集成系统且不知道管理终结点，请与运营商联系。

    ![添加帐户](./media/azure-stack-storage-connect-se/custom-environments.png)

5. 选择“登录”，连接到至少与一个活动 Azure Stack 订阅关联的 Azure Stack 帐户。



6. 选择要使用的 Azure Stack 订阅。 选择“应用”。

    ![帐户管理](./media/azure-stack-storage-connect-se/account-management.png)

    左窗格会显示与所选 Azure Stack 订阅关联的存储帐户。

    ![关联的订阅列表](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>连接到 Azure Stack 存储帐户

还可以使用存储帐户名称和密钥对连接到 Azure Stack 存储帐户。

1. 在存储资源管理器的左窗格中，选择“管理帐户”。 此时会显示你登录的所有 Microsoft 帐户。

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

* [存储资源管理器入门](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack 存储：差异和注意事项](azure-stack-acs-differences.md)
* 若要了解有关 Azure 存储的详细信息，请参阅[Microsoft Azure 存储简介](../../storage/common/storage-introduction.md)
