---
title: "如何使用 Azure 门户授予 MSI 对 Azure 资源的访问权限"
description: "逐步介绍了如何使用 Azure 门户授予一个资源上的 MSI 对另一个资源的访问权限。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9dd02c8d7580cd9233e192f807686b7857ccf696
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---

# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-the-azure-portal"></a>使用 Azure 门户授予托管服务标识 (MSI) 对资源的访问权限

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

为 Azure 资源配置 MSI 后，便可以授予 MSI 对其他资源的访问权限，这一点与安全主体一样。 此示例展示了如何使用 Azure 门户授予 Azure 虚拟机的 MSI 对 Azure 存储帐户的访问权限。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 授予 MSI 对另一个资源的访问权限

在 Azure 资源（[如 Azure VM](msi-qs-configure-portal-windows-vm.md)）上启用 MSI 后：

1. 使用帐户登录 [Azure 门户](https://portal.azure.com)，此帐户与已在其下配置 MSI 的 Azure 订阅相关联。

2. 转到要对其修改访问控制的相应资源。 此示例要授予 Azure VM 对存储帐户的访问权限，所以转到存储帐户。

3. 依次单击资源的“访问控制(IAM)”页和“+ 添加”。 然后，指定“角色”、“将访问权限分配给”（设置为“虚拟机”）、“订阅”和“资源组”（资源所在的资源组）。 在搜索条件区域下，应该会看到资源出现。 选择此资源，并点击“保存”： 

   ![“访问控制(IAM)”屏幕截图](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. 返回到“访问控制(IAM)”主页，其中显示资源 MSI 对应的新条目。 此示例已授予“演示”资源组中“SimpleWinVM”VM 对存储帐户的“参与者”访问权限：

   ![“访问控制(IAM)”屏幕截图](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>故障排除

如果资源的 MSI 未显示在可用标识列表中，请验证是否已正确启用 MSI。 在此示例中，可以返回到 Azure VM，并执行以下操作：

- 查看“配置”页，并确保“MSI 已启用”配置为“是”。
- 查看“扩展”页，确保已成功部署 MSI 扩展。

如果有任一项不正确，可能都需要在资源上再次重新部署 MSI，或排查部署故障。

## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。
- 若要在 Azure VM 上启用 MSI，请参阅[使用 Azure 门户配置 Azure VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)。



