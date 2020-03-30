---
title: 数据引入管道的 DevOps
titleSuffix: Azure Machine Learning
description: 了解如何将 DevOps 实践应用于用于为模型培训准备数据的数据引入管道实现。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247174"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>数据引入管道的 DevOps

在大多数情况下，数据引入解决方案是脚本、服务调用和管道的组成，负责协调所有活动。 在本文中，您将了解如何将 DevOps 实践应用于常见数据引入管道的开发生命周期。 管道为机器学习模型培训准备数据。

## <a name="the-solution"></a>解决方案

请考虑以下数据引入工作流：

![数据引入-管道](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

在此方法中，训练数据存储在 Azure Blob 存储中。 Azure 数据工厂管道从输入 Blob 容器提取数据，转换数据并将数据保存到输出 Blob 容器。 此容器用作 Azure 机器学习服务的[数据存储](concept-data.md)。 数据工厂管道在准备数据后，将调用一个训练机器学习管道来训练模型。 在此特定示例中，数据转换由在 Azure 数据砖块群集上运行的 Python 笔记本执行。 

## <a name="what-we-are-building"></a>我们正在建造什么

与任何软件解决方案一样，有一个团队（例如数据工程师）在研究它。 

![cicd 数据引入](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

它们协作并共享相同的 Azure 资源，如 Azure 数据工厂、Azure 数据块、Azure 存储帐户等。 这些资源的集合是一个开发环境。 数据工程师为相同的源代码库做出了贡献。 持续集成过程将代码组装，使用代码质量测试、单元测试对其进行检查，并生成已测试的代码和 Azure 资源管理器模板等工件。 连续交付过程将工件部署到下游环境。 本文演示如何使用[Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)自动执行 CI 和 CD 进程。

## <a name="source-control-management"></a>源代码管理

团队成员以略有不同的方式协作处理 Python 笔记本源代码和 Azure 数据工厂源代码。 但是，在这两种情况下，代码都存储在源代码管理存储库中（例如，Azure DevOps、GitHub、GitLab），并且协作通常基于某些分支模型（例如[GitFlow）。](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Python 笔记本源代码

数据工程师在 IDE（例如[，可视化工作室代码](https://code.visualstudio.com)）中本地使用 Python 笔记本源代码，或者直接在 Databricks 工作区中使用。 后者提供了在开发环境中调试代码的能力。 在任何情况下，代码都将在分支策略之后合并到存储库。 强烈建议将代码存储在`.py`文件中，而不是以`.ipynb`Jupyter 笔记本格式存储。 它提高了代码的可读性，并在 CI 流程中实现了自动代码质量检查。

### <a name="azure-data-factory-source-code"></a>Azure 数据工厂源代码

Azure 数据工厂管道的源代码是工作区生成的 json 文件的集合。 通常，数据工程师在 Azure 数据工厂工作区中使用可视化设计器，而不是直接使用源代码文件。 使用源代码管理存储库配置工作区，如[Azure 数据工厂文档中](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)所述。 有了此配置，数据工程师能够按照首选分支工作流就源代码进行协作。    

## <a name="continuous-integration-ci"></a>持续集成 （CI）

持续集成过程的最终目标是从源代码收集联合团队工作，并为部署到下游环境做好准备。 与源代码管理一样，对于 Python 笔记本和 Azure 数据工厂管道，此过程是不同的。 

### <a name="python-notebook-ci"></a>Python 笔记本 CI

Python 笔记本的 CI 进程从协作分支（例如，***主控制***或***开发***）获取代码，并执行以下活动：
* 代码林廷
* 单元测试
* 将代码保存为项目

以下代码段演示了在 Azure DevOps ***yaml***管道中这些步骤的实现：

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

管道使用***flake8***执行 Python 代码林点。 它运行源代码中定义的单元测试，并发布林丁和测试结果，以便在 Azure 管道执行屏幕中可用：

![林丁单元测试](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

如果林丁和单元测试成功，管道将复制源代码到项目存储库，以便后续部署步骤使用。

### <a name="azure-data-factory-ci"></a>Azure 数据工厂 CI

Azure 数据工厂管道的 CI 进程是数据引入管道的整个 CI/CD 故事中的瓶颈。 没有***持续***集成。 Azure 数据工厂的可部署项目是 Azure 资源管理器模板的集合。 生成这些模板的唯一方法是单击 Azure 数据工厂工作区中的***发布***按钮。 这里没有自动化。
数据工程师将源代码从他们的功能分支合并到协作分支中，例如，***主控制***或***开发***。 然后，具有授予权限的人员单击***发布***按钮，从协作分支中的源代码生成 Azure 资源管理器模板。 单击该按钮时，工作区将验证管道（将其视为林丁和单元测试），生成 Azure 资源管理器模板（将其视为生成模板），并将生成的模板保存到同一代码存储库***中的技术分支adf_publish（*** 将其视为发布工件）。 此分支由 Azure 数据工厂工作区自动创建。 此过程在[Azure 数据工厂文档中](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)进行了详细介绍。

请务必确保生成的 Azure 资源管理器模板与环境无关。 这意味着所有可能不同于环境的值都被参数化。 Azure 数据工厂足够智能，能够公开大多数此类值作为参数。 例如，在以下模板中，与 Azure 机器学习工作区的连接属性作为参数公开：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

但是，您可能希望公开默认情况下未由 Azure 数据工厂工作区处理的自定义属性。 在本文中，Azure 数据工厂管道调用处理数据的 Python 笔记本。 笔记本接受具有输入数据文件名称的参数。

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

此名称对于***开发人员******、QA、UAT***和***PROD***环境不同。 ***UAT*** 在具有多个活动的复杂管道中，可以有多个自定义属性。 最好在一个位置收集所有这些值并将其定义为管道***变量***：

![adf 变量](media/how-to-cicd-data-ingestion/adf-variables.png)

管道活动在实际使用管道变量时可能引用它们：

![adf 笔记本参数](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

默认情况下，Azure 数据工厂工作区***不会***公开管道变量作为 Azure 资源管理器模板参数。 工作区使用[默认参数化模板](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)，指示应作为 Azure 资源管理器模板参数公开哪些管道属性。 为了将管道变量添加到列表中，请使用以下代码段更新[默认参数化模板](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)的"Microsoft.DataFactory/工厂/管道"部分，并将结果 json 文件放在源文件夹的根目录中：

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

这样做将强制 Azure 数据工厂工作区在单击***发布***按钮时将变量添加到参数列表中：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

json 文件中的值是管道定义中配置的默认值。 部署 Azure 资源管理器模板时，预期目标环境值将覆盖它们。

## <a name="continuous-delivery-cd"></a>连续交付 （CD）

连续交付过程将项目并部署到第一个目标环境。 它确保解决方案通过运行测试工作。 如果成功，它将继续到下一个环境。 CD Azure 管道由表示环境的多个阶段组成。 每个阶段都包含执行以下步骤[的部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)和[作业](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)：
* 将 Python 笔记本部署到 Azure 数据砖块工作区
* 部署 Azure 数据工厂管道 
* 运行管道
* 检查数据引入结果

管道阶段可以配置批准和门，这些[审批](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass)和[门](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops)可对部署过程如何通过环境链演变提供额外控制。

### <a name="deploy-a-python-notebook"></a>部署 Python 笔记本

以下代码段定义将 Python 笔记本复制到 Databricks 群集的 Azure 管道[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)：

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

CI 生成的项目将自动复制到部署代理，并在 ***$（Pipeline.工作区）*** 文件夹中可用。 在这种情况下，部署任务引用包含 Python 笔记本的***di 笔记本***项目。 此[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)使用[数据块 Azure DevOps 扩展名](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)将笔记本文件复制到数据砖块工作区。
***Deploy_to_QA***阶段包含对 Azure DevOps 项目中定义的***devops-ds-qa-vg***变量组的引用。 此阶段的步骤引用此变量组的变量（例如，$（DATABRICKS_URL）、$（DATABRICKS_TOKEN）。 其理念是，下一阶段（例如 ***，Deploy_to_UAT***）将使用在其自己的 UAT 范围变量组中定义的相同变量名称。

### <a name="deploy-an-azure-data-factory-pipeline"></a>部署 Azure 数据工厂管道

Azure 数据工厂的可部署项目是 Azure 资源管理器模板。 因此，它将与***Azure 资源组部署***任务一起部署，如下段代码段所示：

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
数据文件名参数的值来自 QA 阶段变量组中定义的 $（DATA_FILE_NAME） 变量。 同样，可以重写***ARMTemplateForFactory.json***中定义的所有参数。 如果不是，则使用默认值。

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>运行管道并检查数据引入结果

下一步是确保部署的解决方案正常工作。 以下作业定义使用[PowerShell 脚本](https://github.com/microsoft/DataOps/tree/master/adf/utils)运行 Azure 数据工厂管道，并在 Azure 数据砖块群集上执行 Python 笔记本。 笔记本检查数据是否正确引入，并验证结果数据文件的名称为 $（bin_FILE_NAME）。

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

作业中的最后任务检查笔记本执行的结果。 如果它返回错误，它将管道执行的状态设置为失败。

## <a name="putting-pieces-together"></a>将碎片放在一起

本文的结果是 CI/CD Azure 管道，它包括以下阶段：
* CI
* 部署到 QA
    * 部署到数据块 + 部署到 ADF
    * 集成测试

它包含许多***部署***阶段，等于您拥有的目标环境数。 每个***部署***阶段包含两个并行运行[的部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)，以及一个在部署后运行以在环境中测试解决方案的[作业](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)。

管道的示例实现在以下***yaml***片段中组装：

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>后续步骤

* [Azure 数据工厂中的源代码管理](https://docs.microsoft.com/azure/data-factory/source-control)
* [Azure 数据工厂中的持续集成和交付](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [Azure 数据块的 DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
