---
title: 如何在 Azure Dev Spaces 中使用专用 Helm 存储库
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: 在 Azure 开发人员空间中使用专用 Helm 存储库。
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器, Helm
manager: gwallace
ms.openlocfilehash: d0f2cb739a502d614ac329eef1fc57b2fb15cb38
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014511"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>在 Azure Dev Spaces 中使用专用 Helm 存储库

[Helm][helm]是 Kuberentes 的包管理器。 Helm 使用[图表][helm-chart]格式来打包依赖项。 Helm 图表存储在存储库中, 该存储库可以是公共或私有的。 在运行应用程序时, Azure Dev Spaces 仅检索公共存储库中的 Helm 图表。 如果 Helm 存储库是私有的或 Azure Dev Spaces 无法访问它, 则可以将该存储库中的图表直接添加到应用程序。 直接添加图表可让 Azure Dev Spaces 运行应用程序, 而无需访问专用 Helm 存储库。

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>将专用 Helm 存储库添加到本地计算机

使用[helm][helm-repo-add]存储库 "添加" 和 "helm 存储库"[更新][helm-repo-update]从本地计算机访问专用 helm 存储库。

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>向应用程序添加图表

导航到项目的目录, 然后运行`azds prep`。

```cmd
azds prep --public
```

使用你的图表在应用程序的图表目录中创建[yaml][helm-requirements]文件。 例如, 如果应用程序名为*app1*, 则需要创建*图表/app1/yaml*。

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

导航到应用程序的图表目录, 并使用[helm 依赖项更新][helm-dependency-update]更新应用程序的 helm 依赖关系, 并从专用存储库下载该图表。

```cmd
helm dependency update
```

验证包含*tgz*文件的*图表*子目录是否已添加到应用程序的图表目录。 例如,*图表/app1/图表/mychart-0.1.0 tgz*。

已下载专用 Helm 存储库中的图表, 并将其添加到项目。 删除*yaml*文件, 使 Dev 空格不会尝试更新此依赖项。

## <a name="run-your-application"></a>运行应用程序

导航到项目的根目录, 并运行`azds up`以验证应用程序是否已成功地在开发环境中运行。

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

了解有关[Helm 及其工作原理][helm]的详细信息。

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/developing_charts/
[helm-dependency-update]: https://helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/developing_charts/#chart-dependencies