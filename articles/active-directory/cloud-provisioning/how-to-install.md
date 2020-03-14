---
title: 安装 Azure AD Connect 云预配代理
description: 本文介绍如何安装 Azure AD Connect 云设置代理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263343"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>安装 Azure AD Connect 云预配代理
本文档将指导你完成 Azure Active Directory （Azure AD）连接预配代理的安装过程，以及如何在 Azure 门户中进行初始配置。

>[!IMPORTANT]
>以下安装说明假定满足所有[先决条件](how-to-prerequisites.md)。

安装和配置 Azure AD Connect 设置是在以下步骤中完成的：
    
- [安装代理](#install-the-agent)
- [验证代理安装](#verify-agent-installation)


## <a name="install-the-agent"></a>安装代理
若要安装代理，请按照以下步骤操作。

1. 使用企业管理员权限登录到要使用的服务器。
1. 转到 Azure 门户。 在左侧，选择 " **Azure Active Directory**"。
1. 选择 "**管理预配（预览版）** " > **查看所有代理**。
1. 从 Azure 门户下载 Azure AD Connect 预配代理。

   ![下载本地代理](media/how-to-install/install9.png)</br>
1. 运行 Azure AD Connect 预配安装程序（AADConnectProvisioningAgent）。
1. 在**Microsoft Azure AD 连接预配代理包**"屏幕上，接受许可条款，然后选择"**安装**"。

   ![Microsoft Azure AD 连接预配代理包屏幕](media/how-to-install/install1.png)</br>

1. 完成此操作后，配置向导将启动。 使用 Azure AD 全局管理员帐户登录。
1. 在 "**连接 Active Directory** " 屏幕上，选择 "**添加目录**"。 然后，用 Active Directory 管理员帐户登录。 此操作将添加你的本地目录。 选择“**下一页**”。

   ![连接 Active Directory 屏幕](media/how-to-install/install3.png)</br>

1. 在 "**配置完成**" 屏幕上，选择 "**确认**"。 此操作注册并重新启动代理。

   ![配置完成屏幕](media/how-to-install/install4.png)</br>

1. 此操作完成后，应会看到**已成功验证代理配置**的通知。 选择 "**退出**"。

   ![退出按钮](media/how-to-install/install5.png)</br>
1. 如果仍看到初始**Microsoft Azure AD 连接预配代理包**"屏幕，请选择"**关闭**"。

## <a name="verify-agent-installation"></a>验证代理安装
代理验证在 Azure 门户和运行代理的本地服务器上进行。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证
若要验证 Azure 是否正在显示代理，请执行以下步骤。

1. 登录到 Azure 门户。
1. 在左侧，选择 " **Azure Active Directory** > **Azure AD Connect**"。 在中心选择 "**管理预配（预览版）** "。

   ![Azure 门户](media/how-to-install/install6.png)</br>

1.  在 " **Azure AD 预配（预览版）** " 屏幕上，选择 "**查看所有代理**"。

    !["查看所有代理" 选项](media/how-to-install/install7.png)</br>
 
1. 在 "**本地预配代理**" 屏幕上，可以看到已安装的代理。 验证相关的代理是否在该屏幕上，并且标记为“活动”。

   ![本地设置代理屏幕](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>验证端口
若要验证 Azure 是否正在侦听端口443，以及代理是否可以与其通信，请执行以下步骤。

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试验证代理是否可以通过端口443与 Azure 通信。 打开浏览器，并从安装了代理的服务器中转到上一个 URL。

![验证端口可访问性](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本地服务器上
若要验证代理是否正在运行，请执行以下步骤。

1.  使用管理员帐户登录到服务器。
1.  通过导航到服务或通过转到**开始** > **运行** > **services.msc**来打开**服务**。
1.  在 "**服务**" 下，确保**Microsoft Azure AD 连接代理更新**程序和**Microsoft Azure AD 连接设置代理**存在并且其状态为 "*正在运行*"。

    ![服务屏幕](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>已经安装了代理，但必须先对其进行配置和启用，然后才会开始同步用户。 若要配置新代理，请参阅[为 Azure AD Connect 基于云的设置创建新配置](how-to-configure.md)。



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
 
