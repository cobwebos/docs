---
title: 如何在 Azure 开发人员空间中使用专用 Helm 存储库
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: 在 Azure 开发人员空间中使用专用 Helm 存储库。
keywords: 码头机、库伯奈斯、Azure、AKS、Azure 容器服务、容器、头盔
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240472"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>在 Azure 开发人员空间中使用专用 Helm 存储库

[Helm][helm] 是 Kubernetes 的包管理器。 Helm 使用[图表][helm-chart]格式来打包依赖项。 Helm 图表存储在存储库中，存储库可以是公共的，也可以是私有的。 Azure 开发人员空间仅在运行应用程序时从公共存储库检索 Helm 图表。 如果 Helm 存储库是私有的或 Azure 开发人员空间无法访问它，则可以将该存储库中的图表直接添加到应用程序。 直接添加图表可使 Azure 开发人员空间运行应用程序，而无需访问专用 Helm 存储库。

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>将专用 Helm 存储库添加到本地计算机

使用[helm 存储库添加][helm-repo-add]和[掌舵存储库更新][helm-repo-update]从本地计算机访问专用 Helm 存储库。

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>将图表添加到应用程序

导航到项目的目录并运行`azds prep`。

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep` 命令将尝试为项目生成 [Dockerfile 和 Helm 图表](../how-dev-spaces-works-prep.md#prepare-your-code)。 Azure Dev Spaces 使用这些文件来生成和运行代码，但是如果要更改项目的生成和运行方式，则可以修改这些文件。

在应用程序的图表目录中使用图表创建[要求.yaml][helm-requirements]文件。 例如，如果应用程序名为*app1*，则创建*图表/app1/要求。*

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

导航到应用程序的图表目录，并使用[舵依赖项更新][helm-dependency-update]更新应用程序的 Helm 依赖项，并从专用存储库下载图表。

```cmd
helm dependency update
```

验证具有*tgz*文件的*图表*子目录已添加到应用程序的图表目录中。 例如，*图表/app1/图表/mychart-0.1.0.tgz*。

来自私有 Helm 存储库的图表已下载并添加到您的项目中。 删除*要求.yaml*文件，以便开发人员空间不会尝试更新此依赖项。

## <a name="run-your-application"></a>运行应用程序

导航到项目的根目录并运行`azds up`以验证应用程序在开发空间中成功运行。

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>后续步骤

了解更多关于[赫尔姆及其工作原理][helm]。

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
