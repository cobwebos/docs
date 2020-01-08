---
title: 使用 Apache Hive 分析 Twitter 数据 - Azure HDInsight
description: 了解如何使用 HDInsight 中的 Apache Hive 和 Apache Hadoop 将原始 TWitter 数据转换为可搜索的 Hive 表。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435620"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>使用 HDInsight 中的 Apache Hive 和 Apache Hadoop 分析 Twitter 数据

了解如何使用 [Apache Hive](https://hive.apache.org/) 处理 Twitter 数据。 结果是发送最多包含某个特定词的推文的 Twitter 用户列表。

> [!IMPORTANT]  
> 本文档中的步骤已在 HDInsight 3.6 上进行测试。

## <a name="get-the-data"></a>获取数据

Twitter 允许通过 REST API 检索每个推文的数据作为 JavaScript 对象表示法 (JSON) 文档。 要对 API 进行身份验证，需要 [OAuth](https://oauth.net)。

### <a name="create-a-twitter-application"></a>创建 Twitter 应用程序

1. 在 Web 浏览器中登录到 [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/)。 如果没有 Twitter 帐户，请选择 "**立即注册**" 链接。

2. 选择“创建新应用”。

3. 输入“名称”、“说明”、“网站”。 可为“网站”字段补充 URL。 下表显示了一些要使用的示例值：

   | 字段 | 值 |
   |--- |--- |
   | 名称 |MyHDInsightApp |
   | Description |MyHDInsightApp |
   | 网站 |`https://www.myhdinsightapp.com` |

4. 选择 **"是，我同意**"，然后选择 "**创建 Twitter 应用程序**"。

5. 选择 "**权限**" 选项卡。默认权限为**只读**。

6. 选择“密钥和访问令牌”选项卡。

7. 选择 **"创建我的访问令牌"** 。

8. 在页面的右上角选择 "**测试 OAuth** "。

9. 记下“使用者密钥”、“使用者机密”、“访问令牌”和“访问令牌机密”。

### <a name="download-tweets"></a>下载推文

以下 Python 代码会从 Twitter 下载 10,000 篇推文并将其保存到一个名为 **tweets.txt** 的文件中。

> [!NOTE]  
> 由于已安装了 Python，请在 HDInsight 群集上执行以下步骤。

1. 使用[ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使用以下命令安装[Tweepy](https://www.tweepy.org/)、[进度栏](https://pypi.python.org/pypi/progressbar/2.2)和其他所需包：

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. 使用以下命令创建一个名为 **gettweets.py** 的文件：

   ```bash
   nano gettweets.py
   ```

1. 将 `Your consumer secret`、`Your consumer key`、`Your access token`和 `Your access token secret` 替换为 twitter 应用程序的相关信息，以编辑以下代码。 然后，将编辑的代码粘贴为**gettweets.py**文件的内容。

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > 调整最后一行的主题筛选器以跟踪常用关键字。 运行脚本时，使用常用关键字可以更快捕获数据。

1. 使用 **Ctrl+X**，并使用 **Y** 以保存该文件。

1. 使用以下命令运行该文件并下载推文：

    ```bash
    python gettweets.py
    ```

    一个进度指示器会出现。 它会随着推文下载计数到 100%。

   > [!NOTE]  
   > 如果进度栏向前移动需要较长时间，则应更改筛选器以跟踪趋势主题。 如果在筛选器中有很多推文，可以快速获取所需的100推文。

### <a name="upload-the-data"></a>上传数据

要将数据上传到 HDInsight 存储，请使用以下命令：

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

这些命令会在群集中的所有节点都可以访问的位置中存储数据。

## <a name="run-the-hiveql-job"></a>运行 HiveQL 作业

1. 使用以下命令来创建包含 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) 语句的文件：

   ```bash
   nano twitter.hql
   ```

    将以下文本用作文件的内容：

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. 按 **Ctrl+X**，并按 **Y** 以保存该文件。

1. 使用以下命令运行该文件中包含的 HiveQL：

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    此命令会运行 twitter.hql 文件。 查询完成之后，会看到 `jdbc:hive2//localhost:10001/>` 提示符。

1. 根据 beeline 提示，使用以下查询验证数据是否已导入：

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    这会在消息文本中返回最多 10 篇包含 **Azure** 一词的推文。

    > [!NOTE]  
    > 如果已更改 `gettweets.py` 脚本中的筛选器，请将 Azure 替换为用过的筛选器之一。

## <a name="next-steps"></a>后续步骤

已了解如何将非结构化 JSON 数据集转换为结构化的[Apache Hive](https://hive.apache.org/)表。 若要了解有关 HDInsight 上的 Hive 的详细信息，请参阅以下文档：

* [HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [使用 HDInsight 分析航班延误数据](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
