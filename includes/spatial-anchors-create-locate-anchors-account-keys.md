---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 72616d5d79b4dbdb8e14463c706c1626eeb50e97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232438"
---
## <a name="set-up-authentication"></a>设置身份验证

若要访问该服务，需要提供帐户密钥、 访问令牌或 Azure Active Directory 身份验证令牌。

### <a name="account-keys"></a>帐户密钥

帐户密钥是使应用程序能够使用 Azure 空间的定位点服务进行身份验证的凭据。 帐户密钥的使用目的是帮助您快速入门。 尤其是在 Azure 空间的定位点与应用程序的集成的开发阶段。 在这种情况下，您可以通过在开发过程中将它们嵌入在客户端应用程序中使用帐户密钥。 在开发之外，强烈建议将移动到是生产级别支持的访问令牌或 Azure Active Directory 用户身份验证的身份验证机制。 若要获取帐户密钥进行开发，请访问你 Azure 空间的定位点的帐户，并导航到"密钥"选项卡。
