---
title: "Azure Machine Learning Workbench 执行配置文件"
description: "本文档详细介绍 Azure ML Workbench 试验执行的配置设置。"
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: e1356439385cc7fe66985bd2b84e4121386ec23d
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---

# <a name="azure-machine-learning-workbench-execution-configuration-files"></a>Azure Machine Learning Workbench 执行配置文件

当为 Azure Machine Learning (Azure ML) Workbench 提交脚本时，由“aml_config”文件夹中的文件控制执行行为。 此文件夹位于你的项目文件夹根目录下。 请务必了解这些文件的内容，以便以最佳方式实现你期望的执行结果。

以下是此文件夹下的相关文件：
- conda_dependencies.yml
- spark_dependencies.yml
- 计算目标文件
    - \<compute target name>.compute
- 运行配置文件
    - \<run configuration name>.runconfig

>[!NOTE]
>你通常有一个计算目标文件，并为你创建的每个计算目标运行配置文件。 但是，你可以独立地创建这些文件，并且有多个指向同一计算目标的运行配置文件。

## <a name="condadependenciesyml"></a>conda_dependencies.yml
此文件是 [conda 环境文件](https://conda.io/docs/using/envs.html#create-environment-file-by-hand)，它指定 Python 运行时版本以及代码所依赖的包。 当 Azure ML Workbench 在 Docker 容器或 HDInsight 群集中执行脚本时，它将创建用于运行脚本的 [conda 环境](https://conda.io/docs/using/envs.html)。 

在此文件中，指定脚本需要用于执行的 Python 包。 Azure ML Workbench 执行服务根据依赖项列表在 Docker 映像中创建 conda 环境。 此处的包列表必须可供执行引擎访问。 因此，包需要在以下通道中列出：

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* 可公开访问的终结点 (URL)
* 或本地文件路径
* 可供执行引擎访问的其他包

>[!NOTE]
>在 HDInsight 群集上运行时，Azure ML Workbench 将创建仅用于运行的 conda 环境。 这样不同的用户可以在同一群集的不同 python 环境上运行。  

下面是一个典型的 conda_dependencies.yml 文件示例。
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

只要 conda_dependencies.yml 保持不变，Azure ML Workbench 会使用相同的 conda 环境而无需重新生成。 但是，如果此文件中有任何内容发生更改，就会导致重新生成 Docker 映像。

>[!NOTE]
>如果针对本地计算上下文运行执行，则不会使用 conda_dependencies.yml 文件。 需要手动安装本地 Azure ML Workbench Python 环境的包依赖项。

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
在提交 PySpark 脚本和需要安装的 Spark 包时，此文件指定 Spark 应用程序名称。 你还可以指定任何公共 Maven 存储库，以及可在这些 Maven 存储库中找到的 Spark 包。

下面是一个示例：

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>群集优化参数（如辅助角色大小、内核）应置于 spark_dependecies.yml 文件中的“配置”部分 

>[!NOTE]
>如果在 Python 环境中执行该脚本，spark_dependencies.yml文件将被忽略。 只有在针对 Spark（无论是在 Docker 上还是在 HDInsight 群集上）运行时它才会起作用。

## <a name="run-configuration"></a>运行配置
若要指定特定的运行配置，需要一对文件。 它们通常使用 CLI 命令生成。 但也可以克隆现有文件、重命名它们，并对其进行编辑。

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password> --type remotedocker

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> --type cluster
```

此命令创建一对基于指定计算目标的文件。 假设计算目标命名为 foo。 此命令会在你的 aml_config 文件夹生成 foo.compute 和 foo.runconfig。

>[!NOTE]
> 运行配置文件的本地或 docker 名称是任意的。 为方便起见，在你创建一个空白项目时，Azure ML Workbench 会添加这两个运行配置。 你可以重命名附带项目模板的“<run configuration name>.runconfig”文件，或使用任意名称创建新文件。

### <a name="compute-target-namecompute"></a>\<compute target name>.compute
_\<compute target name>.compute_ 文件指定计算目标的连接和配置信息。 它是一个名称/值对列表。 以下是支持的设置。

类型：计算环境类型。 支持的值是：
  - local
  - docker
  - remotedocker
  - cluster

baseDockerImage：用于运行 Python/PySpark 脚本的 Docker 映像。 默认值为 _microsoft/mmlspark:plus-0.7.91_。 我们还支持另一个映像：_microsoft/mmlspark:plus-gpu-0.7.91_，它授予主机的 GPU 访问权限（如果存在 GPU）。

address：虚拟机的 IP 地址或 FQDN（完全限定域名），或 HDInsight 群集头节点。

username：用于访问虚拟机或 HDInsight 头节点的 SSH 用户名。

password：用于 SSH 连接的加密密码。

sharedVolumes：用于指示执行引擎应使用 Docker 共享卷功能来回传送项目文件的标志。 开启此标志可以加快执行，因为 Docker 无需复制可以直接访问项目。 如果 Docker 引擎在 Windows 上运行，则最好设置 false，因为 Windows 上的 Docker 卷共享可能会发生异常。 如果它在 macOS 或 Linux 上运行，则将其设置为 true。

nvidiaDocker：当设置为 true 时，此标志指示 Azure ML Workbench 执行服务使用 nvidia-docker 命令（而不是常规的 docker 命令）来启动 Docker 映像。 nvidia-docker 引擎允许 Docker 容器访问 GPU 硬件。  如果要在 Docker 容器中运行 GPU 执行，则此设置是必需的。 仅 Linux 主机支持 nvidia-docker。 例如，Azure 中基于 Linux 的 DSVM 使用 nvidia docker 进行传送。 Windows 当前不支持 nvidia-docker。

nativeSharedDirectory：此属性指定基目录（例如：_~/.azureml/share/_），可以在其中保存文件，以便在同一计算目标上运行时共享。 如果在 Docker 容器上运行时使用此设置，则 sharedVolumes 必须设置为 true。 否则，执行将失败。

### <a name="run-configuration-namerunconfig"></a>\<run configuration name>.runconfig
_\<run configuration name>.runconfig_ 指定 Azure ML Workbench 的执行行为。 可以配置执行行为，例如跟踪运行历史记录、要使用的计算目标，等等。 运行配置文件的名称用于填充 Azure ML Workbench 桌面应用程序中的执行上下文下拉列表。

ArgumentVector：此部分指定要作为此执行一部分运行的脚本以及该脚本的参数。 例如，如果你在“<run configuration name>.runconfig”文件中有以下代码段 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
“az ml experiment submit foo.runconfig”会自动通过 myscript.py 文件运行该命令，传入 234 作为参数并设置 --verbose 标志。

Target：此参数是 runconfig 文件引用的 .compute 文件的名称。 它通常指向 foo.compute文件，但你可以编辑它以指向不同的计算目标。

Environment Variables：此部分使用户能够将环境变量设置为其运行的一部分。 用户可以使用以下格式的名称/值对来指定环境变量：
```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

可以在用户代码中访问这些环境变量。 例如，此 phyton 代码将打印名为“EXAMPLE_ENV_VAR”的环境变量
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

Framework：此属性指定 Azure ML Workbench 是否应启动 Spark 会话以运行脚本。 默认值为 PySpark。 如果没有运行 PySpark 代码，则将其设置为 Python，这样可以帮助更快地启动工作，并减少开销。

CondaDependenciesFile：此属性指向 aml_config 文件夹中指定 conda 环境依赖项的文件。 如果设置为 null，它将指向默认的 conda_dependencies.yml 文件。

SparkDependenciesFile：此属性指向 aml_config 文件夹中指定 Spark 依赖项的文件。 默认情况下设置为 null，并指向默认 spark_dependencies.yml 文件。

PrepareEnvironment：当设置为 true 时，此属性将指示执行服务根据指定为初始运行一部分的 conda 依赖项来准备 conda 环境。 仅当针对 Docker 环境执行时，此属性才有效。 如果针对本地环境运行，此设置无任何影响。 

TrackedRun：此标志指示执行服务是否跟踪 Azure ML Workbench 运行历史记录基础结构中的运行。 默认值为 _true_。 

UseSampling：UseSampling 指定是否将数据源的活动示例数据集用于运行。 如果设置为 false，数据源将引入和使用从数据存储中读取的完整数据。 如果设置为 true，则使用活动示例。 如果用户想要重写活动示例，则可以使用“DataSourceSettings”来指定要使用的具体示例数据集。 

DataSourceSettings：此配置部分指定数据源设置。 在此部分中，用户指定特定数据源用作运行一部分的现有数据示例。 

以下配置设置指定名为“MySample”的示例用于名为“MyDataSource”的数据源
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

DataSourceSubstitutions：用户想要在不更改代码的情况下从一个数据源切换到另一个数据源时，可以使用数据源替换项。 例如，用户可以通过更改数据源引用，从一个经过简化的本地文件切换到存储在 Azure Blob 中的更大的原始数据集。 当使用替换项时，Azure ML Workbench 将通过引用替换数据源来运行数据源和数据准备包。

下面的示例将 Azure ML 数据源和数据准备包中的“mylocal.datasource”引用替换为“myremote.dsource”。 
 
```
DataSourceSubstitutions:
    myocal.dsource: myremote.dsource
```

根据以上替换，下面的代码示例现在从“myremote.dsource”而不是“mylocal.dsource”进行读取，且用户不需更改其代码。
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>后续步骤
详细了解[执行环境配置](experiment-execution-configuration.md)

