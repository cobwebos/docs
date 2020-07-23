---
title: 安装 Azure AD Connect 云预配代理
description: 本文介绍如何安装 Azure AD Connect 云预配代理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b59942731c8ca7b29de30e160d8370c9cf76ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807632"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>安装 Azure AD Connect 云预配代理
本文档将指导你完成 Azure Active Directory (Azure AD) Connect 预配代理的安装过程，以及如何在 Azure 门户中对其进行初始配置。

>[!IMPORTANT]
>以下安装说明假定已满足所有[先决条件](how-to-prerequisites.md)。

安装和配置 Azure AD Connect 预配通过以下步骤完成：
    
- [安装代理](#install-the-agent)
- [验证代理安装](#verify-agent-installation)


## <a name="install-the-agent"></a>安装代理
若要安装代理，请执行以下步骤。

1. 以企业管理员权限登录到要使用的服务器。
1. 登录到 Azure 门户，然后**Azure Active Directory**"。
1. 在左侧菜单中，选择 " **Azure AD Connect**"。
1. 选择“管理预配(预览版)” > “查看所有代理”。
1. 从 Azure 门户下载 Azure AD Connect 预配代理。

   ![下载本地代理](media/how-to-install/install9.png)</br>
1. 运行 Azure AD Connect 预配安装程序 (AADConnectProvisioningAgent.Installer)。
1. 在“Microsoft Azure AD Connect 预配代理包”屏幕上，接受许可条款，然后选择“安装”。

   ![“Microsoft Azure AD Connect 预配代理包”屏幕](media/how-to-install/install1.png)</br>

1. 此操作完成后，配置向导将启动。 使用 Azure AD 全局管理员帐户登录。
1. 在“Connect Active Directory”屏幕上，选择“添加目录”。 然后使用你的 Active Directory 管理员帐户登录。 此操作将添加本地目录。 选择“**下一页**”。

   ![“Connect Active Directory”屏幕](media/how-to-install/install3.png)</br>

1. 在“配置完成”屏幕上，选择“确认”。 此操作注册并重新启动代理。

   ![“配置完成”屏幕](media/how-to-install/install4.png)</br>

1. 此操作完成后，应该会看到通知“已成功验证代理配置”。 选择“退出”。

   ![“退出”按钮](media/how-to-install/install5.png)</br>
1. 如果仍看到初始“Microsoft Azure AD Connect 预配代理包”屏幕，则选择“关闭”。

## <a name="verify-agent-installation"></a>验证代理安装
代理验证是在 Azure 门户中以及在运行该代理的本地服务器上进行的。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证
若要验证 Azure 是否能够识别到代理，请执行以下步骤。

1. 登录到 Azure 门户。
1. 在左侧选择“Azure Active Directory” > “Azure AD Connect”。 在中心位置选择“管理预配(预览版)”。

   ![Azure 门户](media/how-to-install/install6.png)</br>

1.  在“Azure AD 预配(预览版)”屏幕上，选择“查看所有代理”。

    ![“查看所有代理”选项](media/how-to-install/install7.png)</br>
 
1. 在“本地预配代理”屏幕上，将看到已安装的代理。 验证相关的代理是否在该屏幕上，并且标记为“活动”。

   ![“本地预配代理”屏幕](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>在本地服务器上
若要验证代理是否正在运行，请执行以下步骤。

1.  使用管理员帐户登录到服务器。
1.  导航到“服务”或者转到“开始” > “运行” > “Services.msc”来打开“服务”。
1.  确保“Microsoft Azure AD Connect 代理更新程序”和“Microsoft Azure AD Connect 预配代理”包含在“服务”中，并且其状态为“正在运行”。

    ![“服务”屏幕](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>代理已安装，但是在它开始同步用户之前，必须先对其进行配置和启用。 若要配置新代理，请参阅[为 Azure AD Connect 基于云的预配创建新配置](how-to-configure.md)。



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
 
