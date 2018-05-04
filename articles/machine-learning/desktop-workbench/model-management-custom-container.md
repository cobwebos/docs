---
title: 自定义用于部署 Azure ML 模型的容器映像 | Microsoft Docs
description: 本文介绍如何自定义 Azure 机器学习模型的容器映像
services: machine-learning
author: tedway
ms.author: tedway, raymondl
manager: mwinkle
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: f56b651c40187e42361ac12f0cbf4e509385e0d2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>自定义用于 Azure ML 模型的容器映像

本文介绍如何自定义 Azure 机器学习模型的容器映像。  Azure ML Workbench 使用容器来部署机器学习模型。 模型及其依赖关系一起进行部署，并且 Azure ML 会根据模型、依赖关系及关联文件生成映像。

## <a name="how-to-customize-the-docker-image"></a>如何自定义 Docker 映像
使用以下选项自定义 Azure ML 部署的 Docker 映像：

1. `dependencies.yml` 文件：要管理可从 [PyPi]( https://pypi.python.org/pypi) 安装的依赖关系，可使用 Workbench 项目中的 `conda_dependencies.yml` 文件，或者自己创建。 这是安装可安装 pip 的 Python 依赖关系的建议方法。

   示例 CLI 命令：
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   示例 conda_dependencies 文件： 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Docker 步骤文件：使用此选项，可通过安装不能从 PyPi 安装的依赖关系来自定义已部署映像。 

   该文件应包含 Docker 安装步骤，如同 DockerFile 一样。 文件中允许使用以下命令： 

    RUN, ENV, ARG, LABEL, EXPOSE

   示例 CLI 命令：
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Image、Manifest 和 Service 命令接受 docker 文件标志。

   示例 Docker 步骤文件：
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Azure ML 容器的基本映像是 Ubuntu 且无法更改。 如果指定其他基本映像，将会忽略它。

## <a name="next-steps"></a>后续步骤
现在，你已对容器映像进行了自定义，可将其部署到群集以供大规模使用。  有关为 Web 服务部署设置群集的详细信息，请参阅[模型管理配置](deployment-setup-configuration.md)。 
