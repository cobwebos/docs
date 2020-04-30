---
title: Azure Red Hat OpenShift 4 群集支持策略
description: 了解 Red Hat OpenShift 4 的支持策略要求。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7bdcccee3270f9d2b611682a9a59505158a494d2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205204"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift 支持策略

Azure Red Hat OpenShift 4 群集的某些配置可能会影响群集的可支持性。 Azure Red Hat OpenShift 4 允许群集管理员对内部群集组件进行更改，但并非支持所有更改。 下面的支持策略与 Microsoft 和 Red Hat 的修改违反了策略和支持的功能。

> [!NOTE]
> Azure Red Hat OpenShift 不支持在 OpenShift 容器平台中标记为技术预览版的功能。

## <a name="cluster-configuration-requirements"></a>群集配置要求

* 所有 OpenShift 群集操作员都必须处于托管状态。 可以通过运行`oc get clusteroperators`来返回群集操作员的列表。
* 请勿删除或修改群集 Prometheus 和 Alertmanager 服务。
* 请勿删除 Service Alertmanager 规则。
* 请勿修改 OpenShift 群集版本。
* 请勿删除或修改 Azure Red Hat OpenShift service 日志记录（mdsd pod）。
* 请勿删除或修改 "arosvc.azurecr.io" 群集请求密钥。
* 所有群集虚拟机都必须具有出站 internet 访问权限，至少为 Azure 资源管理器（ARM）和服务日志记录（Geneva）终结点。
* Azure Red Hat OpenShift 服务通过专用链接服务访问群集。  请勿删除或修改服务访问权限。
* 不支持非 RHCOS 计算节点。 例如，不能使用 RHEL 计算节点。

## <a name="supported-virtual-machine-sizes"></a>支持的虚拟机大小

Azure Red Hat OpenShift 4 支持以下虚拟机大小的辅助节点实例：

### <a name="general-purpose"></a>常规用途

|系列|大小|vCPU|内存：GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>内存优化

|系列|大小|vCPU|内存：GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>计算优化

|系列|大小|vCPU|内存：GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|
