---
title: 数据引入管道的 DevOps
titleSuffix: Azure Machine Learning
description: 了解如何将 DevOps 实践应用于用于为模型定型准备数据的数据引入管道实现。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: 49b384e9e2d9b77179a0154bf2d96524c064c2ca
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960966"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>数据引入管道的 DevOps

在大多数情况下，数据引入解决方案是脚本、服务调用和管道协调所有活动的组合。 本文介绍如何将 DevOps 实践应用到常见数据引入管道的开发生命周期。 管道为机器学习模型定型准备数据。

## <a name="the-solution"></a>解决方案

请考虑以下数据引入工作流：

![数据引入管道](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

在此方法中，定型数据存储在 Azure blob 存储中。 Azure 数据工厂管道从输入 blob 容器中提取数据，对数据进行转换，并将数据保存到输出 blob 容器。 此容器作为 Azure 机器学习服务的[数据存储](https://docs.microsoft.com/azure/machine-learning/concept-data#access-data-in-storage)。 准备好数据后，数据工厂管道将调用定型机器学习管道来定型模型。 在此特定示例中，数据转换由 Python 笔记本执行，在 Azure Databricks 群集上运行。 

## <a name="what-we-are-building"></a>正在生成的内容

对于任何软件解决方案，都有一个团队（例如，数据工程师）在其中工作。 

![cicd-数据引入](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

它们协作并共享相同的 Azure 资源，如 Azure 数据工厂、Azure Databricks、Azure 存储帐户等。 这些资源的收集是一个开发环境。 数据工程师参与相同的源代码库。 持续集成过程汇编代码，通过代码质量测试、单元测试和生成项目（如经过测试的代码和 Azure 资源管理器模板）进行检查。 持续交付过程会将项目部署到下游环境。 本文演示如何通过[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)自动执行 CI 和 CD 处理。

## <a name="source-control-management"></a>源代码管理管理

团队成员的工作方式略有不同，可与 Python 笔记本源代码和 Azure 数据工厂源代码进行协作。 但是，在这两种情况下，代码都存储在源代码管理存储库（例如，Azure DevOps、GitHub、GitLab）中，并且协作通常基于某些分支模型（例如， [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)）。

### <a name="python-notebook-source-code"></a>Python 笔记本源代码

数据工程师可以在 IDE 中本地（例如[Visual Studio Code](https://code.visualstudio.com)）或直接在 Databricks 工作区中使用 Python 笔记本源代码。 后者提供调试开发环境上的代码的能力。 在任何情况下，代码将按照分支策略合并到存储库。 强烈建议将代码存储在 `.py` 文件中，而不是 `.ipynb` Jupyter 笔记本格式。 它提高了代码的可读性，并在 CI 过程中启用了自动代码质量检查。

### <a name="azure-data-factory-source-code"></a>Azure 数据工厂源代码

Azure 数据工厂管道的源代码是由工作区生成的 json 文件的集合。 通常，数据工程师使用 Azure 数据工厂工作区中的可视化设计器，而不是直接使用源代码文件。 使用[Azure 数据工厂文档](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)中所述的源代码管理存储库配置工作区。 使用此配置时，数据工程师就可以在首选的分支工作流之后，在源代码中进行协作。    

## <a name="continuous-integration-ci"></a>持续集成 (CI)

持续集成过程的最终目标是从源代码收集联合团队工作，并为部署到下游环境做好准备。 与源代码管理一样，此过程不同于 Python 笔记本和 Azure 数据工厂管道。 

### <a name="python-notebook-ci"></a>Python 笔记本 CI

Python 笔记本的 CI 过程从协作分支（例如， ***master***或***开发***）获取代码并执行以下活动：
* 代码 linting
* 单元测试
* 将代码另存为项目

以下代码片段演示了如何在 Azure DevOps ***yaml***管道中实现这些步骤：

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

管道使用***flake8***来执行 Python 代码 linting。 它运行在源代码中定义的单元测试，并发布 linting 和测试结果，使其在 Azure 管道执行屏幕中可用：

![linting 单元测试](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

如果 linting 和单元测试成功，则管道会将源代码复制到项目存储库，以供后续部署步骤使用。

### <a name="azure-data-factory-ci"></a>Azure 数据工厂 CI

适用于 Azure 数据工厂管道的 CI 过程是数据引入管道的整个 CI/CD 部分的瓶颈。 无***持续***集成。 Azure 数据工厂的可部署项目是 Azure 资源管理器模板的集合。 生成这些模板的唯一方式是单击 "Azure 数据工厂" 工作区中的 "***发布***" 按钮。 这里没有自动化。
数据工程师将源代码从其功能分支合并到协作分支（例如***master***或***开发***）中。 然后，具有授予权限的人员单击 "***发布***" 按钮，从协作分支中的源代码生成 Azure 资源管理器模板。 当单击此按钮时，工作区将对管道进行验证（将其视为 linting 和单元测试），生成 Azure 资源管理器模板（在生成时将其视为），并将生成的模板保存到同一代码存储库中的技术分支***adf_publish*** （将其视为发布项目）。 此分支由 Azure 数据工厂工作区自动创建。 有关此过程的详细信息，请参阅[Azure 数据工厂文档](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)。

请务必确保生成的 Azure 资源管理器模板是环境不可知的。 这意味着，在环境之间可能不同的所有值都是参数化的。 Azure 数据工厂的智能足以将大多数此类值作为参数公开。 例如，在下面的模板中，Azure 机器学习工作区的连接属性作为参数公开：

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

但是，你可能想要公开默认情况下不由 Azure 数据工厂工作区处理的自定义属性。 在本文的方案中，Azure 数据工厂管道调用处理数据的 Python 笔记本。 笔记本接受带有输入数据文件名称的参数。

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

对于***开发***、 ***QA***、 ***UAT***和***生产***环境，此名称是不同的。 在包含多个活动的复杂管道中，可以有多个自定义属性。 最好在一个位置收集所有这些值，并将其定义为管道***变量***：

![adf-变量](media/how-to-cicd-data-ingestion/adf-variables.png)

管道活动在实际使用管道变量时可能会引用它们：

![adf-笔记本-参数](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

默认情况下，Azure 数据工厂工作区***不会***将管道变量作为 azure 资源管理器模板参数公开。 工作区使用[默认参数化模板](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)来决定应将哪些管道属性公开为 Azure 资源管理器模板参数。 若要将管道变量添加到列表中，请将[默认参数化模板](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)的 "DataFactory/工厂/管道" 部分更新为以下代码片段，并将结果 json 文件放置在源文件夹的根目录中：

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

这样做会在单击 "***发布***" 按钮时强制 Azure 数据工厂工作区将变量添加到参数列表中：

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

Json 文件中的值是在管道定义中配置的默认值。 部署 Azure 资源管理器模板时，它们应被目标环境值覆盖。

## <a name="continuous-delivery-cd"></a>持续交付 (CD)

持续交付过程获取项目，并将其部署到第一个目标环境。 它可确保解决方案通过运行测试来运行。 如果成功，它将继续到下一个环境中。 CD Azure 管道由多个表示环境的阶段组成。 每个阶段都包含执行以下步骤的[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)和[作业](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)：
* 将 Python 笔记本部署到 Azure Databricks 工作区
* 部署 Azure 数据工厂管道 
* 运行管道
* 检查数据引入结果

可以为管道阶段配置[审批](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass)和[入口](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops)，以提供对部署过程在环境中的发展方式的其他控制。

### <a name="deploy-a-python-notebook"></a>部署 Python 笔记本

下面的代码段定义了一个将 Python 笔记本复制到 Databricks 群集的 Azure 管道[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)：

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

CI 生成的项目会自动复制到部署代理，并在 ***$ （漏斗）*** 文件夹中提供。 在这种情况下，部署任务是指包含 Python 笔记本的***di-笔记本***项目。 此[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)使用[Databricks Azure DevOps 扩展](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)将笔记本文件复制到 Databricks 工作区。
***Deploy_to_QA***阶段包含对 Azure devops 项目中定义的***devops-vg***变量组的引用。 此阶段中的步骤引用此变量组中的变量（例如，$ （DATABRICKS_URL）、$ （DATABRICKS_TOKEN））。 其思路是，下一阶段（例如***Deploy_to_UAT***）将操作在其自己的 UAT 范围变量组中定义的相同变量名称。

### <a name="deploy-an-azure-data-factory-pipeline"></a>部署 Azure 数据工厂管道

Azure 数据工厂的可部署项目是 Azure 资源管理器模板。 因此，它将随***Azure 资源组部署***任务一起部署，如以下代码片段所示：

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
Data filename 参数的值来自在 QA 阶段变量组中定义的 $ （DATA_FILE_NAME）变量。 同样，可以重写***ARMTemplateForFactory***中定义的所有参数。 如果不是，则使用默认值。

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>运行管道并检查数据引入结果

下一步是确保部署的解决方案能够正常工作。 以下作业定义使用[PowerShell 脚本](https://github.com/microsoft/DataOps/tree/master/adf/utils)运行 Azure 数据工厂管道，并在 Azure Databricks 群集上执行 Python 笔记本。 笔记本检查是否已正确引入数据，并验证结果数据文件是否具有 $ （bin_FILE_NAME）名称。

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

作业中的最后一个任务检查笔记本执行的结果。 如果它返回错误，则将管道执行的状态设置为 "失败"。

## <a name="putting-pieces-together"></a>将各个部分组合在一起

本文的结果是一个 CI/CD Azure 管道，其中包括以下阶段：
* CI
* 部署到 QA
    * 部署到 Databricks 并部署到 ADF
    * 集成测试

它包含的***部署***阶段数等于您拥有的目标环境数。 每个***部署***阶段都包含两个并行运行的[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)和一个在部署后运行的[作业](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)，用于在环境中测试解决方案。

管道的示例实现组合在以下***yaml***代码片段中：

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
* [用于 Azure Databricks 的 DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
