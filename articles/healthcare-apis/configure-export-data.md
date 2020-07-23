---
title: 在 Azure API for FHIR 中配置导出设置
description: 本文介绍如何在 Azure API for FHIR 中配置导出设置。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871017"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>配置导出设置并将数据导出到存储帐户

Azure API for FHIR 支持 $export 命令，使用该命令可将 Azure API for FHIR 帐户中的数据导出到存储帐户。

在 Azure API for FHIR 中执行导出涉及到四个步骤：

1. 在 Azure API for FHIR 服务中启用托管标识
2. 创建 Azure 存储帐户（如果以前尚未创建），并向 Azure API for FHIR 分配对存储帐户的权限
3. 在 Azure API for FHIR 中选择用作导出存储帐户的存储帐户
4. 通过对 Azure API for FHIR 调用 $export 命令来执行导出

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>在 Azure API for FHIR 中启用托管标识

为导出操作配置 Azure API for FHIR 的第一个步骤是在该服务中启用系统范围的托管标识。 可在[此处](../active-directory/managed-identities-azure-resources/overview.md)详细了解 Azure 中的托管标识。

为此，请导航到 Azure API for FHIR 服务，并选择“标识”边栏选项卡。 将状态更改为“打开”会在 Azure API for FHIR 服务中启用托管标识。

![启用托管标识](media/export-data/fhir-mi-enabled.png)

现在可以转到下一步骤，创建一个存储帐户并向该服务分配权限。

## <a name="adding-permission-to-storage-account"></a>添加对存储帐户的权限

导出过程的下一步骤是为 Azure API for FHIR 服务分配对存储帐户的写入权限。

创建存储帐户后，导航到该存储帐户中的“访问控制(IAM)”边栏选项卡，然后选择“添加角色分配”

![启用托管标识](media/export-data/fhir-export-role-assignment.png)

然后，在此处将“存储 Blob 数据参与者”角色添加到服务名称。

![启用托管标识](media/export-data/fhir-export-role-add.png)

我们现已准备好执行下一步骤：在 Azure API for FHIR 中选择用作 $export 的默认存储帐户的存储帐户。

## <a name="selecting-the-storage-account-for-export"></a>为 $export 选择存储帐户

调用 $export 命令之前的最后一个步骤是分配 Azure API for FHIR 使用的要将数据导出到的 Azure 存储帐户。 为此，请在 Azure 门户中导航到 Azure API for FHIR 服务中的“集成”边栏选项卡，并选择存储帐户 

![启用托管标识](media/export-data/fhir-export-storage.png)

然后，我们便可以使用 $export 命令导出数据了。

## <a name="exporting-the-data-using-export-command"></a>使用 $export 命令导出数据

为导出操作配置 Azure API for FHIR 后，可以使用 $export 命令将该服务中的数据导出到指定的存储帐户中。 若要了解如何在 FHIR 服务器中调用 $export 命令，请阅读 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 上有关 $export 规范的文档

> [!IMPORTANT]
> 请注意，Azure API for FHIR 目前仅支持 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 上的导出规范中定义的系统级导出。 此外，目前不支持在 $export 中使用查询参数。

>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)