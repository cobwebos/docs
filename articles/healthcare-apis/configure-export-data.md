---
title: 在 Azure API for FHIR 中配置导出设置
description: 本文介绍如何在 Azure API for FHIR 中配置导出设置。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: e4adceea5c2cd2a36d7a867ca9b9d2ad7c33c155
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91529977"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>配置导出设置并设置存储帐户

Azure API for FHIR 支持 $export 命令，使用该命令可将 Azure API for FHIR 帐户中的数据导出到存储帐户。

在 Azure API for FHIR 中配置导出涉及三个步骤：

1. 在 Azure API for FHIR 服务中启用托管标识
2. 创建 Azure 存储帐户（如果以前尚未创建），并向 Azure API for FHIR 分配对存储帐户的权限
3. 在 Azure API for FHIR 中选择用作导出存储帐户的存储帐户

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>在 Azure API for FHIR 上启用托管标识

配置用于 FHIR 的 Azure API 的第一步是在服务上启用系统范围的托管标识。 可在 [此处](../active-directory/managed-identities-azure-resources/overview.md)阅读有关 Azure 中的托管标识的全部内容。

为此，请导航到 FHIR 服务的 Azure API，并选择 "标识" 边栏选项卡。 将状态更改为 On 将在 Azure API for FHIR Service 中启用托管标识。

![启用托管标识](media/export-data/fhir-mi-enabled.png)

现在，我们可以转到下一步，并创建存储帐户并向我们的服务分配权限。

## <a name="adding-permission-to-storage-account"></a>向存储帐户添加权限

导出的下一步是分配 Azure API for FHIR 服务的权限，以写入存储帐户。

创建存储帐户后，请导航到存储帐户中 (IAM) 边栏选项卡上的 "访问控制"，并选择 "添加角色分配"

![导出角色分配](media/export-data/fhir-export-role-assignment.png)

然后，将角色存储 Blob 数据参与者添加到我们的服务名称。

![添加角色](media/export-data/fhir-export-role-add.png)

现在，我们已准备好进行下一步，我们可以在 Azure API for FHIR 中选择存储帐户作为 $export 的默认存储帐户。

## <a name="selecting-the-storage-account-for-export"></a>为 $export 选择存储帐户

最后一步是分配 azure API for FHIR 用于将数据导出到的 Azure 存储帐户。 为此，请导航到 Azure 门户中的 Azure API for FHIR service 中的集成边栏选项卡，并选择存储帐户

![FHIR 导出存储](media/export-data/fhir-export-storage.png)

之后，我们就可以使用 $export 命令导出数据了。

>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)
