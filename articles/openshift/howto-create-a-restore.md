---
title: 使用 Velero 创建 Azure Red Hat OpenShift 4 群集应用程序还原
description: 了解如何使用 Velero 创建 Azure Red Hat OpenShift 群集应用程序的还原
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: 9eac34d643ba0df4be79a064858c580c884de727
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078555"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>创建 Azure Red Hat OpenShift 4 群集应用程序还原

本文介绍如何准备环境，以创建 Azure Red Hat OpenShift 4 群集应用程序还原。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 安装必备组件并安装所需的工具
> * 创建 Azure Red Hat OpenShift 4 应用程序还原

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>开始之前

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>创建 Azure Red Hat OpenShift 4 应用程序备份

若要创建 Azure Red Hat OpenShift 4 应用程序备份，请参阅 [创建 Azure Red Hat OpenShift 4 备份](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>还原 Azure Red Hat OpenShift 4 应用程序

这些步骤将允许你还原以前使用 Velero 备份的应用程序。
你可以检查群集当前已识别的备份列表，以查看可用于还原的备份。  若要执行此步骤，需要执行以下命令：

_ 此步骤 (假设已在名为 "Velero" 的项目中安装 Velero ) _

```bash
oc get backups -n velero
```

拥有要还原的备份后，需要使用以下命令执行还原操作：

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

此步骤将创建在创建备份时从上一步骤中备份的 Kubernetes 对象。

若要查看还原的状态，请执行以下步骤：

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
当阶段显示时 `Completed` ，应还原 Azure Red Hat 4 应用程序。

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>使用包含的快照还原 Azure Red Hat OpenShift 4 应用程序


若要使用 Velero 创建包含持久卷的 Azure Red Hat OpenShift 4 应用程序的还原，需要使用以下命令执行还原操作：

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
此步骤将创建在创建备份时从上一步骤中备份的 Kubernetes 对象。

若要查看还原的状态，请执行以下步骤：

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
当阶段显示时 `Completed` ，应还原 Azure Red Hat 4 应用程序。

有关如何使用 Velero 创建备份和还原的详细信息，请参阅以 [本机方式备份 OpenShift 资源](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Red Hat OpenShift 4 群集应用程序。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Velero 创建 OpenShift v4 群集应用程序还原
> * 使用 Velero 创建包含快照的 OpenShift v4 群集应用程序还原


转到下一篇文章，了解 Azure Red Hat OpenShift 4 支持的资源。

* [Azure Red Hat OpenShift v4 支持的资源](supported-resources.md)


