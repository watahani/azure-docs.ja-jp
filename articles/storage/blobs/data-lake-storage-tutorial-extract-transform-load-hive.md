---
title: チュートリアル:Azure HDInsight の Apache Hive を使用して抽出、変換、読み込み (ETL) 操作を実行する
description: このチュートリアルでは、生の CSV データセットからデータを抽出し、Azure HDInsight の Apache Hive を使用してデータを変換した後、Sqoop を使用して変換済みデータを Azure SQL Database に読み込む方法について説明します。
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jamesbak
ms.openlocfilehash: 65d2d69c788a54371664d1a443a79bd121332470
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105153"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-apache-hive-on-azure-hdinsight"></a>チュートリアル:Azure HDInsight の Apache Hive を使用したデータの抽出、変換、および読み込み

このチュートリアルでは、ETL (データの抽出、変換、読み込み) 操作を実行します。 生の CSV データ ファイルを取得して Azure HDInsight クラスターにインポートした後、Apache Hive を使用して変換し、Apache Sqoop を使用して Azure SQL データベースに読み込みます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * データを抽出し、HDInsight クラスターにアップロードする。
> * Apache Hive を使用してデータを変換する。
> * Sqoop を使用して Azure SQL データベースにデータを読み込む。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* **HDInsight での Linux ベースの Hadoop クラスター**。新しい Linux ベースの HDInsight クラスターを作成する方法については、[Hadoop、Spark、Kafka などによる HDInsight のクラスターの設定](./data-lake-storage-quickstart-create-connect-hdi-cluster.md)に関するページを参照してください。

* **Azure SQL Database**:保存先データ ストアとして Azure SQL Database を使用します。 SQL データベースがない場合は、「[Azure Portal で Azure SQL データベースを作成する](../../sql-database/sql-database-get-started.md)」を参照してください。

* **Azure CLI**:Azure CLI をインストールしていない場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

* **SSH クライアント**:詳細については、[SSH を使用した HDInsight (Hadoop) への接続](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

> [!IMPORTANT]
> この記事の手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、Azure HDInsight バージョン 3.4 以降で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

### <a name="download-the-flight-data"></a>フライト データのダウンロード

このチュートリアルでは、運輸統計局からのフライト データを使用して ETL 操作を実行する方法を示します。 チュートリアルを完了するには、このデータをダウンロードする必要があります。

1. [米国運輸省研究・革新技術庁/運輸統計局](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)のページに移動します。

1. このページで、次の値を選択します。

   | Name | 値 |
   | --- | --- |
   | **Filter Year (フィルター処理する年)** |2013 |
   | **Filter Period (フィルターの期間)** |January |
   | **フィールド** |Year、FlightDate、UniqueCarrier、Carrier、FlightNum、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |

1. その他のフィールドはすべてクリアします。

1. **[Download]** を選択します。 選択したデータ フィールドを含む .ZIP ファイルがダウンロードされます。

## <a name="extract-and-upload-the-data"></a>データの抽出とアップロード

このセクションでは、`scp` を使用してデータを HDInsight クラスターにアップロードします。

コマンド プロンプトを開き、次のコマンドを使用して HDInsight クラスターのヘッド ノードに .zip ファイルをアップロードします。

```bash
scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
```

* \<FILE_NAME> を .ZIP ファイルの名前に置き換えます。
* \<SSH_USER_NAME> を HDInsight クラスターの SSH ログインに置き換えます。
* \<CLUSTER_NAME> を HDInsight クラスターの名前に置き換えます。

パスワードを使用して SSH ログインを認証する場合は、パスワードを入力するよう求められます。 

公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーへのパスを指定することが必要な場合があります。 たとえば、「 `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:` 」のように入力します。

アップロードが完了したら、SSH を使用してクラスターに接続します。 コマンド プロンプトで次のコマンドを入力します。

```bash
ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
```

次のコマンドを使用して .zip ファイルを解凍します。

```bash
unzip <FILE_NAME>.zip
```

このコマンドで、約 60 MB の **.csv** ファイルが抽出されます。

次のコマンドを使用してディレクトリを作成し、*.csv* ファイルをそのディレクトリにコピーします。

```bash
hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
```

次のコマンドを使用して、Data Lake Storage Gen2 ファイル システムを作成します。

```bash
hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
```

## <a name="transform-the-data"></a>データの変換

このセクションでは、Beeline を使用して Apache Hive ジョブを実行します。

Apache Hive ジョブの一環として、.csv ファイルから **delays** という名前の Apache Hive テーブルにデータをインポートします。

HDInsight クラスター用に既に開いている SSH プロンプトから、次のコマンドを使用して **flightdelays.hql** という名前の新しいファイルを作成して編集します。

```bash
nano flightdelays.hql
```

このファイルの内容として、次のテキストを使用します。

```hiveql
 DROP TABLE delays_raw;
 -- Creates an external table over the csv file
 CREATE EXTERNAL TABLE delays_raw (
    YEAR string,
    FL_DATE string,
    UNIQUE_CARRIER string,
    CARRIER string,
    FL_NUM string,
    ORIGIN_AIRPORT_ID string,
    ORIGIN string,
    ORIGIN_CITY_NAME string,
    ORIGIN_CITY_NAME_TEMP string,
    ORIGIN_STATE_ABR string,
    DEST_AIRPORT_ID string,
    DEST string,
    DEST_CITY_NAME string,
    DEST_CITY_NAME_TEMP string,
    DEST_STATE_ABR string,
    DEP_DELAY_NEW float,
    ARR_DELAY_NEW float,
    CARRIER_DELAY float,
    WEATHER_DELAY float,
    NAS_DELAY float,
    SECURITY_DELAY float,
    LATE_AIRCRAFT_DELAY float)
 -- The following lines describe the format and location of the file
 ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
 LINES TERMINATED BY '\n'
 STORED AS TEXTFILE
 LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

-- Drop the delays table if it exists
DROP TABLE delays;
-- Create the delays table and populate it with data
-- pulled in from the CSV file (via the external table defined previously)
CREATE TABLE delays
LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
AS
SELECT YEAR AS year,
    FL_DATE AS flight_date,
    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
    ORIGIN_AIRPORT_ID AS origin_airport_id,
    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
    DEST_AIRPORT_ID AS dest_airport_id,
    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
    substring(DEST_CITY_NAME,2) AS dest_city_name,
    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
    DEP_DELAY_NEW AS dep_delay_new,
    ARR_DELAY_NEW AS arr_delay_new,
    CARRIER_DELAY AS carrier_delay,
    WEATHER_DELAY AS weather_delay,
    NAS_DELAY AS nas_delay,
    SECURITY_DELAY AS security_delay,
    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
FROM delays_raw;
```

ファイルを保存するには、Esc キーを押した後、「`:x`」を入力します。

Hive を起動し、**flightdelays.hql** ファイルを実行するには、次のコマンドを使用します。

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
```

__flightdelays.hql__ スクリプトの実行が完了したら、次のコマンドを使用して対話型 Beeline セッションを開きます。

```bash
beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
```

`jdbc:hive2://localhost:10001/>` プロンプトが表示されたら、次のクエリを使用してインポートされたフライト遅延データからデータを取得します。

```hiveql
INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
SELECT regexp_replace(origin_city_name, '''', ''),
    avg(weather_delay)
FROM delays
WHERE weather_delay IS NOT NULL
GROUP BY origin_city_name;
```

このクエリにより、悪天候による遅延が発生した都市の一覧と平均遅延時間が取得され、`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` に保存されます。 その後、Sqoop がこの場所からデータを読み取り、Azure SQL Database にエクスポートします。

Beeline を終了するには、プロンプトで「 `!quit` 」と入力します。

## <a name="create-a-sql-database-table"></a>SQL データベース テーブルの作成

この操作には、SQL データベースのサーバー名が必要になります。 サーバー名を確認するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. **[SQL データベース]** を選択します。

1. 使用するデータベースの名前でフィルター処理します。 サーバー名は **[サーバー名]** 列に表示されます。

1. 使用するデータベースの名前でフィルター処理します。 サーバー名は **[サーバー名]** 列に表示されます。

    ![Azure SQL サーバーの詳細を取得](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Azure SQL サーバーの詳細を取得")

    SQL Database に接続してテーブルを作成するには、多くの方法があります。 次の手順では、HDInsight クラスターから [FreeTDS](http://www.freetds.org/) を使用します。

FreeTDS をインストールするには、クラスターへの SSH 接続から次のコマンドを使用します。

```bash
sudo apt-get --assume-yes install freetds-dev freetds-bin
```

インストールが完了したら、次のコマンドを使用して SQL Database サーバーに接続します。

* \<SERVER_NAME> は SQL Database サーバー名に置き換えてください。
* \<ADMIN_LOGIN> は SQL Database の管理者ログインに置き換えてください。
* \<DATABASE_NAME> はデータベース名に置き換えてください。

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
```

メッセージが表示されたら、SQL Database 管理者ログインのパスワードを入力します。

次のテキストのような出力が返されます。

```
locale is "en_US.UTF-8"
locale charset is "UTF-8"
using default charset "UTF-8"
Default database being set to sqooptest
1>
```

`1>` プロンプトで、次のステートメントを入力します。

```hiveql
CREATE TABLE [dbo].[delays](
[origin_city_name] [nvarchar](50) NOT NULL,
[weather_delay] float,
CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
([origin_city_name] ASC))
GO
```

`GO` ステートメントを入力すると、前のステートメントが評価されます。
このクエリにより、クラスター化インデックス付きの、**delays** という名前のテーブルが作成されます。

次のクエリを使用して、テーブルが作成されたことを確認します。

```hiveql
SELECT * FROM information_schema.tables
GO
```

出力は次のテキストのようになります。

```
TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
databaseName       dbo             delays        BASE TABLE
```

Enter `exit` at the `1>`」と入力して、tsql ユーティリティを終了します。

## <a name="export-and-load-the-data"></a>データのエクスポートと読み込み

これまでのセクションで、変換済みデータを `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` という場所にコピーしました。 このセクションでは、Sqoop を使用して、`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` のデータを、Azure SQL データベースに作成したテーブルにエクスポートします。

次のコマンドを使用して、Sqoop が SQL データベースを認識できることを確認します。

```bash
sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
```

このコマンドにより、**delays** テーブルを作成したデータベースを含む、データベースの一覧が返されます。

次のコマンドを使って、**hivesampletable** テーブルから **delays** テーブルにデータをエクスポートします。

```bash
sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
```

Sqoop は **delays** テーブルを含むデータベースに接続して、`/tutorials/flightdelays/output` ディレクトリから **delays** テーブルにデータをエクスポートします。

`sqoop` コマンドが完了したら、tsql ユーティリティを使用してデータベースに接続します。

```bash
TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
```

次のステートメントを使用して、データが **delays** テーブルにエクスポートされたことを確認します。

```sql
SELECT * FROM delays
GO
```

テーブル内のデータの一覧が表示されます。 テーブルには、都市の名前と、その都市のフライトの平均遅延時間が含まれます。

「`exit`」と入力して、tsql ユーティリティを終了します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで使用されるすべてのリソースは、既存のものです。 クリーンアップは必要ありません。

## <a name="next-steps"></a>次の手順

HDInsight でのデータ操作の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Databricks を使用してデータの抽出、変換、読み込みを行う](./data-lake-storage-use-hdi-cluster.md)
