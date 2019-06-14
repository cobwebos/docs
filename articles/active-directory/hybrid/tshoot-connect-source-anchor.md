---
title: Azure AD Connect：在安装期间排查源定位点问题 | Microsoft Docs
description: 本主题提供在安装期间排查源定位点问题的步骤。
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114148"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>在安装期间排查源定位点问题
本文解释了安装期间可能会出现的各种源定位点相关问题，并提供了这些问题的解决方法。

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Azure Active Directory 中的源定位点无效

### <a name="custom-installation"></a>自定义安装

在自定义安装期间，Azure AD Connect 将从 Azure Active Directory 读取源定位点策略。 如果该策略在 Azure Active Directory 中存在，则 Azure AD Connect 会应用该策略，除非客户已替代策略。 向导会告知已读取哪些属性。 此外，当你尝试替代源定位点策略时，向导会发出警告。

在执行此读取操作期间，Azure Active Directory 中的源定位点策略可能不符合预期。 在这种情况下，Azure AD Connect 不知道要使用哪个源定位点，并需要手动替代。</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

若要解决此问题，可以通过选择特定的属性来手动替代源定位点。 当且仅当你确定要选择哪个属性时，才继续使用此选项。 如果不确定，请联系 [Microsoft 支持人员](https://support.microsoft.com/contactus/)获取指导。 如果更改源定位点策略，将会破坏本地用户及其关联 Azure 资源之间的关联。</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>快速安装
在快速安装期间，Azure AD Connect 将从 Azure Active Directory 读取源定位点策略。 如果该策略在 Azure Active Directory 中存在，Azure AD Connect 将应用该策略。 没有任何选项可用于执行手动替代。

在执行此读取操作期间，Azure Active Directory 中的源定位点策略可能不符合预期。 在这种情况下，Azure AD Connect 不知道要使用哪个源定位点。</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

若要解决此问题，需要使用自定义模式重新安装，并通过选择特定的属性来手动替代源定位点。 当且仅当你确定要选择哪个属性时，才继续使用此选项。 如果不确定，请联系 [Microsoft 支持人员](https://support.microsoft.com/contactus/)获取指导。 如果更改源定位点策略，将会破坏本地用户及其关联 Azure 资源之间的关联。

### <a name="invalid-source-anchor-in-sync-engine"></a>同步引擎中的源定位点无效
在安装期间，Azure AD Connect 可能会尝试使用无效的源定位点配置同步引擎。 此操作很可能与某个产品问题相关，此时 Azure AD Connect 安装将会失败。 如果遇到此问题，请联系 [Microsoft 支持人员](https://support.microsoft.com/contactus/)。</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。