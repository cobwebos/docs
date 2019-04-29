---
title: Azure AD Connect：在安装过程中对源定位点问题进行故障排除 |Microsoft Docs
description: 本主题提供有关如何在安装过程中对源定位点的问题进行故障排除步骤。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114148"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>源定位点安装过程中的故障排除问题
此文章介绍了不同源定位点相关的安装和产品/服务以解决这些问题的方法中可能发生的问题。

## <a name="invalid-source-anchor-in-azure-active-directory"></a>在 Azure Active Directory 中的无效的源定位点

### <a name="custom-installation"></a>自定义安装

自定义在安装期间，Azure AD Connect 从 Azure Active Directory 读取源定位点策略。 如果该策略在 Azure Active Directory 中存在，Azure AD Connect 将适用相同的策略，除非客户中重写。 向导会通知你读取的属性。 此外，向导将警告如果您尝试重写源定位点策略。

在此读取操作时，就可以在 Azure Active Directory 中的源定位点策略是意外。 在这种情况下，Azure AD Connect 不知道哪些源定位点来使用和需要手动重写。</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

若要解决此问题，您可以通过选择特定属性手动替代源定位点。 使用此选项继续操作，当且仅当你确信要选择的属性。 如果您不能确定，请联系[Microsoft 支持部门](https://support.microsoft.com/contactus/)有关的指南。 如果您更改源定位点策略，它会中断你的本地用户和其关联的 Azure 资源之间的关联。</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>快速安装
Express 安装期间，Azure AD Connect 从 Azure Active Directory 读取源定位点策略。 如果该策略在 Azure Active Directory 中存在，Azure AD Connect 应用相同的策略。 没有要执行手动重写的选项。

在此读取操作时，就可以在 Azure Active Directory 中的源定位点策略是意外。 在这种情况下，Azure AD Connect 不知道源定位点应该是什么。</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

若要解决此问题，需要重新安装使用自定义模式，并通过选择特定的属性手动重写源定位点。 使用此选项继续操作，当且仅当你确信要选择的属性。 如果您不能确定，请联系[Microsoft 支持部门](https://support.microsoft.com/contactus/)有关的指南。 如果您更改源定位点策略，它会中断你的本地用户和其关联的 Azure 资源之间的关联。

### <a name="invalid-source-anchor-in-sync-engine"></a>同步引擎中的无效的源定位点
在安装期间，它是可能的 Azure AD 连接尝试配置同步引擎使用无效的源定位点。 此操作很可能是一个产品问题，Azure AD Connect 的安装将失败。 请联系[Microsoft 支持部门](https://support.microsoft.com/contactus/)如果中运行与此问题。</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。