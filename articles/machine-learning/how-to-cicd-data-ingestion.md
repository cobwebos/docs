---
title: 数据引入管道的 DevOps
titleSuffix: Azure Machine Learning
description: 了解如何将 DevOps 做法应用到为模型训练准备数据的数据引入管道实现。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247174"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>数据引入管道的 DevOps

在大多数方案中，数据引入解决方案是脚本、服务调用以及协调所有活动的管道的复合体。 本文介绍如何将 DevOps 做法应用到常用数据引入管道的开发生命周期。 管道为机器学习模型训练准备数据。

## <a name="the-solution"></a>解决方案

考虑以下数据引入工作流：

![data-ingestion-pipeline](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

在此方法中，训练数据存储在 Azure Blob 存储中。 Azure 数据工厂管道从输入 Blob 容器提取数据，转换数据，然后将数据保存到输出 Blob 容器。 此容器充当 Azure 机器学习服务的[数据存储](concept-data.md)。 准备好数据后，数据工厂管道将调用训练机器学习管道来训练模型。 在此特定示例中，数据转换由 Azure Databricks 群集上运行的 Python 笔记本执行。 

## <a name="what-we-are-building"></a>生成的内容

与任何软件解决方案一样，会有某个团队（例如“数据工程师”）处理此过程。 

![cicd-data-ingestion](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

他们相互协作并共享相同的 Azure 资源，例如 Azure 数据工厂、Azure Databricks、Azure 存储帐户等。 这些资源的集合构成了开发环境。 数据工程师为同一源代码库贡献代码。 持续集成过程汇编代码，使用代码质量测试和单元测试检查代码，并生成经过测试的代码和 Azure 资源管理器模板等项目。 持续交付过程将项目部署到下游环境。 本文演示如何使用 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 将 CI 和 CD 过程自动化。

## <a name="source-control-management"></a>源代码管理

团队成员的工作方式略有不同，他们针对 Python 笔记本源代码和 Azure 数据工厂源代码展开协作。 但是，在这两种情况下，代码都存储在源代码管理存储库（例如 Azure DevOps、GitHub、GitLab）中，协作通常是基于某个分支模型（例如 [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)）展开的。

### <a name="python-notebook-source-code"></a>Python 笔记本源代码

数据工程师可以在 IDE（例如 [Visual Studio Code](https://code.visualstudio.com)）本地或者直接在 Databricks 工作区中处理 Python 笔记本源代码。 使用后一种方式可以在开发环境中调试代码。 在任一情况下，代码都会根据分支策略合并到存储库中。 强烈建议将代码存储在 `.py` 文件中，而不要以 `.ipynb` Jupyter 笔记本格式存储。 这样可以改善代码的可读性，并在 CI 过程中实现自动代码质量检查。

### <a name="azure-data-factory-source-code"></a>Azure 数据工厂源代码

Azure 数据工厂管道的源代码是工作区生成的 JSON 文件的集合。 通常，数据工程师会在 Azure 数据工厂工作区中使用可视化设计器，而不是直接处理源代码文件。 根据 [Azure 数据工厂文档](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration)中所述，使用源代码管理存储库配置工作区。 完成此配置后，数据工程师可以在运行首选的分支工作流之后，针对源代码展开协作。    

## <a name="continuous-integration-ci"></a>持续集成 (CI)

持续集成过程的最终目标是从源代码收集联合团队的工作，并为部署到下游环境做好准备。 与源代码管理一样，此过程对于 Python 笔记本和 Azure 数据工厂管道是不同的。 

### <a name="python-notebook-ci"></a>Python 笔记本 CI

Python 笔记本的 CI 过程从协作分支（例如 ***master*** 或 ***develop***）获取代码并执行以下活动：
* 代码检查
* 单元测试
* 将代码保存为项目

以下代码片段演示如何在 Azure DevOps ***yaml*** 管道中实现这些步骤：

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

该管道使用 ***flake8*** 执行 Python 代码检查。 它运行源代码中定义的单元测试，并发布检查和测试结果以便在 Azure 管道执行屏幕中显示：

![linting-unit-tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

如果检查和单元测试成功，管道会将源代码复制到项目存储库，供后续的部署步骤使用。

### <a name="azure-data-factory-ci"></a>Azure 数据工厂 CI

Azure 数据工厂管道的 CI 过程是数据引入管道的整个 CI/CD 历程中的瓶颈。 不会执行持续集成。 Azure 数据工厂的可部署项目是 Azure 资源管理器模板的集合。 生成这些模板的唯一方式是单击 Azure 数据工厂工作区中的“发布”按钮。 此处不提供自动化功能。
数据工程师将源代码从其功能分支合并到协作分支（例如 ***master*** 或 ***develop***）。 然后，已被授予权限的人员可以单击“发布”按钮，从协作分支中的源代码生成 Azure 资源管理器模板。 单击此按钮时，工作区将验证管道（将其视为检查和单元测试），生成 Azure 资源管理器模板（将其视为生成），并将生成的模板保存到同一代码存储库中的技术分支 ***adf_publish***（将其视为发布项目）。 此分支由 Azure 数据工厂工作区自动创建。 [Azure 数据工厂文档](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)中详细介绍了此过程。

必须确保生成的 Azure 资源管理器模板不区分环境。 这意味着，在不同环境中可能不同的所有值将会参数化。 Azure 数据工厂足够智能，可将大多数此类值作为参数公开。 例如，在以下模板中，Azure 机器学习工作区的连接属性将作为参数公开：

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

但是，你可能想要公开 Azure 数据工厂工作区默认不会处理的自定义属性。 在本文所述的方案中，Azure 数据工厂管道将调用一个处理数据的 Python 笔记本。 该笔记本接受包含输入数据文件名的参数。

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

此名称对于 ***Dev***、***QA***、***UAT*** 和 ***PROD*** 环境是不同的。 在包含多个活动的复杂管道中，可能存在多个自定义属性。 最好是将所有这些值收集到一个位置，并将其定义为管道变量：

![adf-variables](media/how-to-cicd-data-ingestion/adf-variables.png)

管道活动在实际使用管道变量时可以引用它们：

![adf-notebook-parameters](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Azure 数据工厂工作区默认不会将管道变量作为 Azure 资源管理器模板参数公开。 工作区使用[默认参数化模板](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)，指明应将哪些管道属性作为 Azure 资源管理器模板参数公开。 若要将管道变量添加到列表中，请使用以下代码片段更新[默认参数化模板](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template)的“Microsoft.DataFactory/factories/pipelines”节，并将结果 JSON 文件放在源文件夹的根目录中：

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

这样，在单击“发布”按钮时，会强制 Azure 数据工厂工作区将变量添加到参数列表中：

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

JSON 文件中的值是在管道定义中配置的默认值。 部署 Azure 资源管理器模板时，这些值预期会由目标环境值重写。

## <a name="continuous-delivery-cd"></a>持续交付 (CD)

持续交付过程提取项目并将其部署到第一个目标环境。 它通过运行测试来确保解决方案可正常运行。 如果测试成功，则继续部署到下一个环境。 CD Azure 管道由多个表示环境的阶段组成。 每个阶段包含[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)，以及执行以下步骤的[作业](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)：
* 将 Python 笔记本部署到 Azure Databricks 工作区
* 部署 Azure 数据工厂管道 
* 运行管道
* 检查数据引入结果

可以使用[审批](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass)和[门限](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops)（就部署过程如何在环境链中递进提供额外的控制）来配置管道阶段。

### <a name="deploy-a-python-notebook"></a>部署 Python 笔记本

以下代码片段定义一个将 Python 笔记本复制到 Databricks 群集的 Azure 管道[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)：

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

CI 生成的项目将自动复制到部署代理，并在 ***$(Pipeline.Workspace)*** 文件夹中提供。 在本例中，部署任务引用包含 Python 笔记本的 ***di-notebooks*** 项目。 此[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)使用 [Databricks Azure DevOps 扩展](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)将笔记本文件复制到 Databricks 工作区。
***Deploy_to_QA*** 阶段包含对 Azure DevOps 项目中定义的 ***devops-ds-qa-vg*** 变量组的引用。 此阶段中的步骤引用此变量组中的变量（例如 $(DATABRICKS_URL), $(DATABRICKS_TOKEN)）。 其思路是，下一阶段（例如 ***Deploy_to_UAT***）将使用其自身 UAT 范围的变量组中定义的相同变量名称运行。

### <a name="deploy-an-azure-data-factory-pipeline"></a>部署 Azure 数据工厂管道

Azure 数据工厂的可部署项目是一个 Azure 资源管理器模板。 因此，它将通过“Azure 资源组部署”任务来部署，如以下代码片段所示：

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
数据文件名参数的值取自 QA 阶段变量组中定义的 $(DATA_FILE_NAME) 变量。 同样，可以重写 ***ARMTemplateForFactory.json*** 中定义的所有参数。 如果未重写，则使用默认值。

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>运行管道并检查数据引入结果

下一步是确保部署的解决方案可正常运行。 以下作业定义使用 [PowerShell 脚本](https://github.com/microsoft/DataOps/tree/master/adf/utils)运行 Azure 数据工厂管道，并在 Azure Databricks 群集上执行一个 Python 笔记本。 该笔记本检查是否已正确引入数据，并验证名称为 $(bin_FILE_NAME) 的结果数据文件。

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

作业中的最后一个任务检查笔记本执行结果。 如果返回了错误，则将管道执行状态设置为 failed。

## <a name="putting-pieces-together"></a>将各个部分组合到一起

本文的结果是由以下阶段构成的 CI/CD Azure 管道：
* CI
* 部署到 QA
    * 部署到 Databricks + 部署到 ADF
    * 集成测试

此管道包含许多“部署”阶段，阶段数目与现有的目标环境数目相同。 每个“部署”阶段包含两个并行运行的[部署](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)，以及一个在部署后运行的、用于在环境中测试解决方案的[作业](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml)。

以下 ***yaml*** 代码片段中汇编了该管道的示例实现：

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
* [Azure 数据工厂中的持续集成和持续交付](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [Azure Databricks 的 DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
