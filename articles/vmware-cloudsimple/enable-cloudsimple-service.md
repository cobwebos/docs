---
title: CloudSimple 服务启用 Azure VMware 解决方案
description: 介绍如何启用 Azure 订阅上的 CloudSimple 服务，然后注册 CLoudSimple 资源提供程序
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676933"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>注册你的 Azure 订阅上的 Microsoft.VMwareCloudSimple 资源提供程序

CloudSimple 服务，可使用 Azure CloudSimple VMware 解决方案。 若要使用 CloudSimple 服务，它必须首先将上启用你的 Azure 订阅。 然后，可以将 Microsoft.VMwareCloudSimple 服务作为资源提供程序注册。

## <a name="enable-the-cloudsimple-service"></a>启用 CloudSimple 服务

若要启用你的 Azure 订阅上的 CloudSimple 服务，请打开包含的支持请求[Microsoft 支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 提交请求时，请选择以下选项。

* 问题类型：**技术**
* 订阅：**你的订阅 ID**
* 服务类型：**通过 CloudSimple VMware 解决方案**
* 问题类型：**专用的节点配额**
* 问题子类型：**增加专用节点的配额**
* 使用者：**启用 CloudSimple 服务**

你还可以联系 Microsoft 客户代表处[ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com)。 提供你的电子邮件中的 Azure 订阅 ID。  

为你的订阅启用了 CloudSimple 服务后，可以启用订阅上的资源提供程序。

## <a name="register-the-resource-provider"></a>注册资源提供程序

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“所有服务”  。

3. 搜索并选择**订阅**。

    ![选择订阅](media/cloudsimple-service-select-subscriptions.png)

4. 选择你想要启用 CloudSimple 服务的订阅。

5. 单击**资源提供程序**的订阅。

6. 使用**Microsoft.VMwareCloudSimple**以筛选资源提供程序。

7. 选择资源提供程序，然后单击**注册**。

    ![注册资源提供程序](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 CloudSimple 服务](create-cloudsimple-service.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)