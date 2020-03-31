---
title: 安装 Azure AD Connect 云预配代理
description: 本文介绍如何安装 Azure AD 连接云预配代理。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263343"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>安装 Azure AD Connect 云预配代理
本文档将引导您完成 Azure 活动目录 （Azure AD） 连接预配代理的安装过程以及如何在 Azure 门户中最初配置它。

>[!IMPORTANT]
>以下安装说明假定已满足所有[先决条件](how-to-prerequisites.md)。

安装和配置 Azure AD 连接预配在以下步骤中完成：
    
- [安装代理](#install-the-agent)
- [验证代理安装](#verify-agent-installation)


## <a name="install-the-agent"></a>安装代理
要安装代理，请按照以下步骤操作。

1. 登录到您将使用企业管理员权限的服务器。
1. 转到 Azure 门户。 在左侧，选择**Azure 活动目录**。
1. 选择 **"管理预配（预览）"** > **查看所有代理**。
1. 从 Azure 门户下载 Azure AD 连接预配代理。

   ![下载本地代理](media/how-to-install/install9.png)</br>
1. 运行 Azure AD 连接预配安装程序（AADConnect 预配代理.安装程序）。
1. 在**Microsoft Azure AD 连接预配代理包屏幕上**，接受许可条款并选择 **"安装**"。

   ![微软 Azure AD 连接预配代理包屏幕](media/how-to-install/install1.png)</br>

1. 此操作完成后，配置向导将启动。 使用 Azure AD 全局管理员帐户登录。
1. 在 **"连接活动目录"** 屏幕上，选择 **"添加目录**"。 然后使用活动目录管理员帐户登录。 此操作将添加本地目录。 选择“下一步”。

   ![连接活动目录屏幕](media/how-to-install/install3.png)</br>

1. 在 **"配置完整**"屏幕上，选择 **"确认**"。 此操作注册并重新启动代理。

   ![配置完成屏幕](media/how-to-install/install4.png)</br>

1. 此操作完成后，您应该会看到已**成功验证代理配置的通知。** 选择**退出**。

   ![“退出”按钮](media/how-to-install/install5.png)</br>
1. 如果仍然看到初始的**Microsoft Azure AD 连接预配代理包**屏幕，请选择 **"关闭**"。

## <a name="verify-agent-installation"></a>验证代理安装
代理验证发生在 Azure 门户和运行代理的本地服务器上。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证
要验证 Azure 正在看到代理，请按照以下步骤操作。

1. 登录到 Azure 门户。  
1. 在左侧，选择**Azure 活动目录** > **Azure AD 连接**。 在中心中，选择 **"管理预配（预览）"。**

   ![Azure 门户](media/how-to-install/install6.png)</br>

1.  在**Azure AD 预配（预览）** 屏幕上，选择 **"查看所有代理**"。

    ![查看所有代理选项](media/how-to-install/install7.png)</br>
 
1. 在**本地预配代理**屏幕上，可以看到您安装的代理。 验证相关的代理是否在该屏幕上，并且标记为“活动”。**

   ![本地预配代理屏幕](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>验证端口
要验证 Azure 正在侦听端口 443 以及代理是否可以与它通信，请按照以下步骤操作。

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试验证代理是否可以通过端口 443 与 Azure 通信。 打开浏览器，然后从安装代理的服务器转到以前的 URL。

![端口可到达性验证](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本地服务器上
要验证代理是否正在运行，请按照以下步骤操作。

1.  使用管理员帐户登录到服务器。
1.  通过导航到服务或访问**启动** > **运行** > **服务.msc**来打开**服务**。
1.  在 **"服务**"下，请确保**Microsoft Azure AD 连接代理更新程序**以及**Microsoft Azure AD 连接预配代理**已存在，并且其状态正在*运行*。

    ![服务屏幕](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>代理已安装，但必须先配置并启用它才能开始同步用户。 要配置新代理，请参阅[为 Azure AD 连接基于云的预配创建新配置](how-to-configure.md)。



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
 
