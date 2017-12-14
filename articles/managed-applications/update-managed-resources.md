---
title: "更新 Azure 托管应用程序中的资源 | Microsoft Docs"
description: "介绍如何处理 Azure 托管应用程序的托管资源组中的资源。"
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d3c955d7be0e7e6d45751c0e685bad498e524d94
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>处理 Azure 托管应用程序的托管资源组中的资源

本文介绍如何更新已部署为托管应用程序的一部分的资源。 托管应用程序的发布者有权访问托管资源组中的资源。 若要更新这些资源，需要找到与托管应用程序关联的托管资源组，并访问该资源组中的资源。

本文假设已部署[包含 Azure 管理服务的托管 Web 应用程序 (IaaS)](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) 示例项目中的托管应用程序。 该托管应用程序包含一个 **Standard_D1_v2** 虚拟机。 如果尚未部署该托管应用程序，仍可以使用本文来熟悉更新托管资源组的步骤。

下图显示了已部署的托管应用程序。

![已部署的托管应用程序](./media/update-managed-resources/deployed.png)

在本文中，我们使用 Azure CLI 来执行以下操作：

* 识别托管应用程序
* 识别托管资源组
* 识别托管资源组中的虚拟机资源
* 更改 VM 大小（更改为未被使用的更小大小，或者更改为更大的大小以支持更多负载）
* 将用于指定允许位置的策略分配到托管资源组

## <a name="get-managed-application-and-managed-resource-group"></a>获取托管应用程序和托管资源组

若要获取资源组中的托管应用程序，请使用：

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

若要获取托管资源组的 ID，请使用：

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>调整托管资源组中 VM 的大小

若要查看托管资源组中的虚拟机，请提供托管资源组的名称。

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

若要更新 VM 的大小，请使用：

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

完成操作后，验证应用程序是否在 Standard D2 v2 大小的 VM 上运行。

![使用 Standard D2 v2 大小的托管应用程序](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>将策略应用到托管资源组

获取托管资源组并在该范围分配策略。 策略 **e56962a6-4747-49cd-b67b-bf8b01975c4c** 是用于指定允许位置的内置策略。

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

若要查看允许的位置，请使用：

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

策略分配显示在门户中。

![查看策略分配](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](overview.md)。
* 有关示例项目，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。
