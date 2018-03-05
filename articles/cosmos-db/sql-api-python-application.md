---
title: "适用于 Azure Cosmos DB 的 Python Flask Web 应用程序教程 | Microsoft Docs"
description: "查看一个数据库教程，其中介绍了使用 Azure Cosmos DB 从 Azure 上托管的 Python Flask Web 应用程序来存储和访问数据。 查找应用程序开发解决方案。"
keywords: "应用程序开发, Python Flask, Python Web 应用程序, Python Web 开发"
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: cosmos-db
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/23/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2150b91b5c8dd0326893497fe963fbe1d7cc59bd
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>构建使用 Azure Cosmos DB 的 Python Flask Web 应用程序
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

本教程介绍如何使用 Azure Cosmos DB 通过 Azure 应用服务中托管的 Python Flask 应用程序存储和访问数据。 本教程假设读者在 Python 和 Azure 网站的用法方面具有一定的经验。

本数据库教程涵盖以下内容：

1. 创建并预配 Azure Cosmos DB 帐户。
2. 创建 Python Flask 应用程序。
3. 从 Web 应用程序连接并使用 Azure Cosmos DB。
4. 将 Web 应用程序部署到 Azure 应用服务。

通过学习本教程，将可以构建一个可对轮询进行投票的简单投票应用程序。

![屏幕截图：本数据库教程创建的投票应用程序](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>数据库教程先决条件
在按照本文中的说明操作之前，应确保已安装下列项：

* [一个 Azure 订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 已启用 **Azure 开发**和 **Python 开发**的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。 可以检查是否已安装这些必备组件，并在本地打开“Visual Studio 安装程序”进行安装。   
* [Microsoft Azure SDK for Python 2.7](https://azure.microsoft.com/downloads/)。 
* [Python 2.7](https://www.python.org/downloads/windows/)。 可以使用 32 位或 64 位安装。

> [!IMPORTANT]
> 如果首次安装 Python 2.7，请确保在自定义 Python 2.7.13 屏幕中，选择“向路径添加 python.exe”。
> 
> ![自定义 Python 2.7.11 屏幕的屏幕截图，需要在该屏幕中选择“向路径添加 python.exe”](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Microsoft Visual C++ Compiler for Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266)。

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>步骤 1：创建 Azure Cosmos DB 数据库帐户
让我们首先创建一个 Azure Cosmos DB 帐户。 如果已有一个帐户，或者要在本教程中使用 Azure Cosmos DB 模拟器，可以跳到[步骤 2：创建新的 Python Flask 应用程序](#step-2-create-a-new-python-flask-web-application)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
现在，我们演练如何从头开始新建 Python Flask Web 应用程序。

## <a name="step-2-create-a-new-python-flask-web-application"></a>步骤 2：新建 Python Flask Web 应用程序
1. 在 Visual Studio 的“文件”菜单中，指向“新建”，并单击“项目”。
   
    将显示“新建项目”对话框。
2. 在左窗格中，依次展开“模板”、“Python”，并单击“Web”。 
3. 在中心窗格中选择“Flask Web 项目”，并在“名称”框中，键入“tutorial”，然后单击“确定”。 请记住，Python 包的名称应全部为小写，如 [Style Guide for Python Code（Python 代码风格指南）](https://www.python.org/dev/peps/pep-0008/#package-and-module-names)中所述。
   
    对于新接触 Python Flask 的人员，它是一个 Web 应用程序开发框架，可帮助你更快地在 Python 中构建 Web 应用程序。
   
    ![Visual Studio 中“新建项目”窗口的屏幕截图，截图上包括左侧突出显示的 Python、中间已选中的 Python Flask Web 项目以及“名称”框中的名称“教程”](./media/sql-api-python-application/image9.png)
4. 在“Python Tools for Visual Studio”窗口中，单击“安装到虚拟环境中”。 
   
    ![数据库教程 - Python Tools for Visual Studio 窗口的屏幕截图](./media/sql-api-python-application/python-install-virtual-environment.png)
5. 在“添加虚拟环境”窗口中选择“Python 2.7”或“Python 3.5”，在“选择解释器”框中接受其他默认值，并单击“创建”。 此操作将设置项目所需的 Python 虚拟环境。
   
    ![数据库教程 - Python Tools for Visual Studio 窗口的屏幕截图](./media/sql-api-python-application/image10_A.png)
   
    成功安装环境后，输出窗口会显示 `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.`

## <a name="step-3-modify-the-python-flask-web-application"></a>步骤 3：修改 Python Flask Web 应用程序
### <a name="add-the-python-flask-packages-to-your-project"></a>将 Python Flask 包添加到项目
设置项目后，需要将所需的 Flask 包（包括 pydocumentdb - 用于 Azure Cosmos DB SQL API 的 Python 包）添加到项目。

1. 在解决方案资源管理器中，打开名为 **requirements.txt** 的文件并将内容替换为：
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. 保存 **requirements.txt** 文件。 
3. 在解决方案资源管理器中，右键单击“env”，并单击“使用 requirements.txt 安装”。
   
    ![显示 env (Python 2.7) 已选中的屏幕截图，其中突出显示了列表中的“使用 requirements.txt 安装”](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    安装成功后，输出窗口会显示以下信息：
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > 在相当少见的情况下，可能会在输出窗口中看到失败。 如果出现此情况，请检查错误是否与清理相关。 有时候清理会失败，但安装仍是成功的（在输出窗口中向上滚动以确认这一点）。 可以通过 [验证虚拟环境](#verify-the-virtual-environment)来检查安装。 如果安装失败，但验证成功，则可以继续操作。
   > 
   > 

### <a name="verify-the-virtual-environment"></a>验证虚拟环境
我们必须确保正确安装所有内容。

1. 按 **Ctrl**+**Shift**+**B** 生成解决方案。
2. 生成成功后，按 **F5**启动网站。 这会启动 Flask 开发服务器，并启动 Web 浏览器。 应该看到以下页面。
   
    ![在浏览器中显示的空 Python Flask Web 开发项目](./media/sql-api-python-application/image12.png)
3. 在 Visual Studio 中按 **Shift**+**F5** 停止调试网站。

### <a name="create-database-collection-and-document-definitions"></a>创建数据库、集合和文档定义
现在，通过添加新文件并更新其他文件来创建投票应用程序。

1. 在解决方案资源管理器中，右键单击“教程”项目，单击“添加”，并单击“新建项”。 选择“空 Python 文件”并将该文件命名为 **forms.py**。  
2. 将以下代码添加到 forms.py 文件，然后保存该文件。

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>将所需的导入添加到 views.py 中
1. 在“解决方案资源管理器”中，展开 **tutorial** 文件夹并打开 **views.py** 文件。 
2. 将以下导入语句添加到 **views.py** 文件的顶部，然后保存该文件。 这些语句将导入 Azure Cosmos DB 的 PythonSDK 和 Flask 包。
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>创建数据库、集合和文档
* 还是在 **views.py**中，将以下代码添加到文件末尾。 这会创建窗体使用的数据库。 不要删除 **views.py**中任何现有的代码。 仅将其追加到末尾。

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>读取数据库、集合、文档，并提交窗体
* 还是在 **views.py**中，将以下代码添加到文件末尾。 这会设置窗体、读取数据库、集合和文档。 不要删除 **views.py**中任何现有的代码。 仅将其追加到末尾。

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>创建 HTML 文件
1. 在解决方案资源管理器中的 **tutorial** 文件夹中，右键单击 **templates** 文件夹，并依次单击“添加”、“新建项”。 
2. 选择“HTML 页”，并在名称框中键入 **create.html**。 
3. 重复步骤 1 和步骤 2，以创建另外两个 HTML 文件：results.html 和 vote.html。
4. 将以下代码添加到 `<body>` 元素中的 **create.html**。 它会显示一条消息，说明我们创建了新的数据库、集合和文档。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. 将以下代码添加到 `<body`> 元素中的 **results.html**。 它会显示轮询结果。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. 将以下代码添加到 `<body`> 元素中的 **vote.html**。 它会显示轮询并接受投票。 注册投票时，控件权会传递到 views.py 中，Azure Cosmos DB 会在该位置识别投票并相应地追加文档。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. 在 **templates** 文件夹中，将 **index.html** 的内容替换为以下内容。 这会作为应用程序的登录页。
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>添加配置文件并更改 \_\_init\_\_.py
1. 在解决方案资源管理器中，右键单击 **tutorial** 项目，单击“添加”，再单击“新建项”，选择“空 Python 文件”，然后将该文件命名为 **config_cosmos.py**。 Flask 中的窗体需要此配置文件。 也可将其用于提供密钥。 但此教程不需要此密钥。
2. 将以下代码添加到 config_cosmos.py，需要在下一步更改 **COSMOSDB\_HOST** 和 **COSMOSDB\_KEY** 的值。
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. 在 [Azure 门户](https://portal.azure.com/)中，依次单击“浏览”、“Azure Cosmos DB 帐户”导航到“密钥”页，双击要使用的帐户名，并单击“概要”区域中的“密钥”按钮。 在“密钥”页中，复制“URI”值并将其粘贴到“config.py”文件中，作为 COSMOSDB\_HOST 属性的值。 
4. 返回到 Azure 门户，在“密钥”页中，复制“主密钥”或“辅助密钥”的值，并将其粘贴到“config_cosmos.py”文件，作为 COSMOSDB\_KEY 属性的值。
5. 在 **\_\_init\_\_.py** 文件中，添加以下行以包括配置文件读取和一些基本日志记录功能： 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    因此，该文件的内容应为：
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. 添加所有文件后，解决方案资源管理器应如下所示：
   
    ![Visual Studio 解决方案资源管理器窗口的屏幕截图](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>步骤 4：本地运行 Web 应用程序
1. 按 **Ctrl**+**Shift**+**B** 生成解决方案。
2. 生成成功后，按 **F5**启动网站。 应会在屏幕上看到以下内容：
   
    ![在 Web 浏览器中显示的 Python + Azure Cosmos DB 投票应用程序的屏幕截图](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. 单击“创建/清除投票数据库”  以生成数据库。
   
    ![Web 应用程序 – 开发详细信息的创建页面的屏幕截图](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. 然后，单击“投票”  并选择选项。
   
    ![提出了一个投票问题的 Web 应用程序的屏幕截图](./media/sql-api-python-application/cosmos-db-vote.png)
5. 对于所投的每一票，它都增加了相应的计数器。
   
    ![投票页面所示的结果的屏幕截图](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. 按 Shift+F5 停止调试该项目。

## <a name="step-5-deploy-the-web-application-to-azure"></a>步骤 5：将 Web 应用程序部署到 Azure
创建可在本地针对 Azure Cosmos DB 正常工作的完整应用程序后，我们要创建一个 web.config 文件，将服务器上的文件更新为与本地环境匹配，然后在 Azure 中查看已完成的应用。 此过程只能在 Visual Studio 2017 中执行。 如果使用其他 Visual Studio 版本，请参阅[发布到 Azure 应用服务](/visualstudio/python/publishing-to-azure.md)。

1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击项目，并选择“添加”>“新建项...”。在显示的对话框中，选择“Azure web.config (Fast CGI)”模板，再选择“确定”。 随后会在项目根目录中创建一个 `web.config` 文件。 

2. 修改 `web.config` 中的 `<system.webServer>` 节，使路径与 Python 安装匹配。 例如，对于 Python 2.7 x64，该条目应如下所示：
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. 将 `web.config` 中的 `WSGI_HANDLER` 条目设置为 `tutorial.app`，使之与项目名称匹配。 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. 在 Visual Studio 的“解决方案资源管理器”中，展开 **tutorial** 文件夹，右键单击 `static` 文件夹，并依次选择“添加”>“新建项...”、“Azure 静态文件 web.config”模板、“确定”。 此操作会在 `static` 中创建另一个 `web.config`，用于针对该文件夹禁用 Python 处理。 此配置会将静态文件的请求发送到默认的 Web 服务器，而不使用 Python 应用程序。

5. 保存文件，右键单击解决方案资源管理器中的项目（确保不要在本地运行它），并选择“发布”。  
   
     ![解决方案资源管理器中选中的教程的屏幕截图，其中突出显示了“发布”选项](./media/sql-api-python-application/image20.png)
6. 在“发布”对话框中，依次选择“Microsoft Azure 应用服务”和“新建”，然后单击“发布”。
   
    ![“发布 Web 窗口”的屏幕截图，其中突出显示了 Microsoft Azure 应用服务](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. 在“创建应用服务”对话框中，输入 Web 应用名称、订阅、资源组和应用服务计划，然后单击“创建”。
   
    ![“Microsoft Azure Web 应用窗口”窗口的屏幕截图](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. 几秒钟后，Visual Studio 会完成将文件复制到服务器的过程，并 在 `http://<your app service>.azurewebsites.net/` 页上显示“由于发生内部服务器错误，无法显示该页”。

9. 在 Azure 门户中打开新的应用服务帐户，在导航菜单中向下滚动到“开发工具”部分，选择“扩展”，并单击“+ 添加”。

10. 在“选择扩展”页上，向下滚动到最近的 Python 2.7 安装并选择 x86 或 x64 位选项，然后单击“确定”接受法律条款。  
   
11. 使用 Kudu 控制台（可通过 `https://<your app service name>.scm.azurewebsites.net/DebugConsole` 访问）安装应用的 `requirements.txt` 文件中所列的包。 为此，请在 Kudu 诊断控制台中导航到 Python 文件夹 `D:\home\Python27`，并根据 [Kudu 控制台](/visual-studio/python/managing-python-on-azure-app-service.md#azure-app-service-kudu-console)部分中所述运行以下命令：

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. 安装新包后，按“重启”按钮在 Azure 门户中重启应用服务。 

    > [!Tip] 
    > 如果对应用的 `requirements.txt` 文件做了任何更改，请务必重新使用 Kudu 控制台来安装该文件中现已列出的所有包。 

13. 完全配置服务器环境后，请在浏览器中刷新页面，随后应会显示该 Web 应用。

    ![将 Bottle、Flask 和 Django 应用发布到应用服务后的结果](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > 如果该网页未出现，或者仍然出现“由于发生内部服务器错误，无法显示该页” 消息，请在 Kudo 中打开 web.config 文件，将 ` <httpErrors errorMode="Detailed"></httpErrors>` 添加到 system.webServer 节，并刷新页面。 这样就会在浏览器中提供详细的错误输出。 

## <a name="troubleshooting"></a>故障排除
如果这是你在计算机上运行的第一个 Python 应用，请确保下列文件夹（或对应的安装位置）包括在 PATH 变量中：

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

如果在投票页上收到了错误，并且已将项目命名为 **tutorial** 以外的名称，请确保 **\_\_init\_\_.py** 引用以下行中正确的项目名称：`import tutorial.view`。

## <a name="next-steps"></a>后续步骤
祝贺你！ 现已完成第一个使用 Azure Cosmos DB 的 Python Web 应用程序并将其发布到了 Azure。

要将其他功能添加到 Web 应用程序，请查看 [Azure Cosmos DB Python SDK](sql-api-sdk-python.md)中提供的 API。

有关 Azure、Visual Studio 和 Python 的详细信息，请参阅 [Python 开发人员中心](https://azure.microsoft.com/develop/python/)。 

有关其他 Python Flask 教程，请参阅 [The Flask Mega-Tutorial, Part I: Hello, World!（Flask 大型教程，第 I 部分：Hello, World!）](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)。 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Azure portal]: http://portal.azure.com
