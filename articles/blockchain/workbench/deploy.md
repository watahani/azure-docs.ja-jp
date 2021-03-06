---
title: Azure Blockchain Workbench を展開する
description: Azure Blockchain Workbench を展開する方法
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 2c5e3664a9ef26319eb8a51d03ddef6a5392ffa9
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102994"
---
# <a name="deploy-azure-blockchain-workbench"></a>Azure Blockchain Workbench を展開する

Azure Blockchain Workbench は、Azure Marketplace のソリューション テンプレートを使って展開されます。 テンプレートを使うと、ブロックチェーン アプリケーションの作成に必要なコンポーネントを簡単に展開できます。 展開が済むと、Blockchain Workbench を使ってクライアント アプリにアクセスし、ユーザーとブロックチェーン アプリケーションを作成および管理できます。

Blockchain Workbench のコンポーネントについて詳しくは、「[Azure Blockchain Workbench アーキテクチャ](architecture.md)」をご覧ください。

## <a name="prepare-for-deployment"></a>デプロイの準備をする

Blockchain Workbench を使用すると、ブロックチェーン台帳を、ブロックチェーンベース アプリケーションの構築に最もよく使用される関連 Azure サービスのセットと共にデプロイできます。 Blockchain Workbench をデプロイすると、Azure サブスクリプションのリソース グループ内に次の Azure サービスがプロビジョニングされます。

* 1 Event Grid トピック
* 1 Service Bus 名前空間
* 1 Application Insights
* 1 SQL Database (Standard S0)
* 2 App Services (Standard)
* 2 Azure Key Vault
* 2 Azure Storage アカウント (Standard LRS)
* 2 仮想マシン スケール セット (検証コントロールおよび worker ノードの場合)
* 2 Virtual Network (各仮想ネットワークにロード バランサー、ネットワーク セキュリティ グループ、パブリック IP アドレスが含まれます)
* 省略可能:Azure Monitor

次に **myblockchain** リソース グループ内に作成されるデプロイの例を次に示します。

![デプロイ例](media/deploy/example-deployment.png)

Blockchain Workbench のコストは、基礎となる Azure サービスのコストの総計です。 Azure サービスの料金情報は、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して計算できます。

> [!IMPORTANT]
> Azure Free レベル サブスクリプションなど、サービス制限のあるサブスクリプションをご利用の場合、VM コアのクォータ不足が原因でデプロイに失敗することがあります。 デプロイの前に、「[仮想マシンの vCPU クォータ](../../virtual-machines/windows/quotas.md)」の記事のガイダンスに従ってクォータをチェックしてください。 既定の VM を選択した場合は、6 つの VM コアが必要です。 それより小さいサイズの VM (*Standard DS1 v2* など) に変更した場合、必要なコア数は 4 つに減ります。

## <a name="prerequisites"></a>前提条件

Azure Blockchain Workbench では、Azure AD 構成とアプリケーション登録が必要です。 デプロイの前に Azure AD を[手動で構成](#azure-ad-configuration)するか、デプロイ後にスクリプトを実行するかを選択できます。 Blockchain Workbench を再デプロイしている場合は、「[Azure AD 構成](#azure-ad-configuration)」を参照して Azure AD 構成を確認します。

> [!IMPORTANT]
> Workbench は、Azure AD アプリケーションを登録するために使用しているテナントと同じテナントに展開する必要はありません。 Workbench は、リソースを展開するための十分なアクセス許可があるテナントに展開する必要があります。 Azure AD テナントについて詳しくは、「[Azure Active Directory テナントを取得する方法](../../active-directory/develop/quickstart-create-new-tenant.md)」および「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)」をご覧ください。



## <a name="deploy-blockchain-workbench"></a>Blockchain Workbench を展開する

前提条件の手順が完了すると、Blockchain Workbench を展開できる状態になります。 次のセクションでは、フレームワークを展開する方法の概要を説明します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 右上隅でお使いのアカウントを選び、Azure Blockchain Workbench を展開する Azure AD テナントに切り替えます。
3. 左側のウィンドウで、**[リソースの作成]** を選びます。 **[Search the Marketplace]** 検索バーで、`Azure Blockchain Workbench` を検索します。 

    ![Marketplace の検索バー](media/deploy/marketplace-search-bar.png)

4. **Azure Blockchain Workbench** を選びます。

    ![Marketplace の検索結果](media/deploy/marketplace-search-results.png)

5. **作成**を選択します。
6. 基本的な設定を行います。

    ![Azure Blockchain Workbench を作成する](media/deploy/blockchain-workbench-settings-basic.png)

    | Setting | 説明  |
    |---------|--------------|
    | Resource prefix (リソース プレフィックス) | この展開の短い一意識別子です。 この値は、リソースの名前付けのベースとして使われます。 |
    | VM ユーザー名 | このユーザー名は、すべての仮想マシン (VM) の管理者として使われます。 |
    | 認証の種類 | VM への接続にパスワードまたはキーを使うかどうかを選びます。 |
    | パスワード | このパスワードは、VM に接続するために使われます。 |
    | SSH | **ssh-rsa** で始まる単一行形式の RSA 公開キー、または複数行の PEM 形式を使います。 SSH キーは、Linux と OS X では `ssh-keygen` を使って、Windows では PuTTYGen を使って、生成できます。 SSH キーについて詳しくは、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」をご覧ください。 |
    | Database password (データベース パスワード) / Confirm database password (データベース パスワードの確認) | 展開の一部として作成されるデータベースにアクセスするために使うパスワードを指定します。 |
    | Deployment region (展開するリージョン) | Blockchain Workbench リソースを展開する場所を指定します。 最善の可用性を得るには、**[場所]** 設定と一致させる必要があります。 |
    | サブスクリプション | 展開に使う Azure サブスクリプションを指定します。 |
    | リソース グループ | **[新規作成]** を選び、一意のリソース グループ名を指定して、新しいリソース グループを作成します。 |
    | 場所 | フレームワークを展開するリージョンを指定します。 |

7. **[OK]** を選んで、基本設定の構成セクションを完了します。

8. **[詳細設定]** で、新しいブロックチェーン ネットワークを作成するか、または既存の Proof-of-Authority ブロックチェーン ネットワークを使用するかを選択します。

    **[新規作成]** の場合:

    *[新規作成]* を選択した場合、単一メンバーのサブスクリプション内に一連の Ethereum Proof-of Authority (PoA) ノードが作成されます。 

    ![新しいブロック チェーン ネットワークの詳細設定](media/deploy/advanced-blockchain-settings-new.png)

    | Setting | 説明  |
    |---------|--------------|
    | 監視 | Blockchain ネットワークを監視するために Azure Monitor を有効にするかどうかを選択します。 |
    | Azure Active Directory の設定 | **[後で追加]** を選択します。</br>注:[Azure AD の事前構成](#azure-ad-configuration)を選択した場合、または再デプロイしている場合は、*[今すぐ追加]* を選択します。 |
    | VM の選択 | Blockchain ネットワークの望ましい VM サイズを選びます。 Azure Free レベルなどサービス制限のあるサブスクリプションを使用している場合は、より小さい VM サイズ (*Standard DS1 v2* など) を選択してください。 |

    **[既存のものを使用]** の場合:

    *[既存のものを使用]* を選択した場合、Ethereum Proof-of-Authority (PoA) ブロックチェーン ネットワークを指定できます。 エンドポイントの要件は次のとおりです。

    * エンドポイントは、Ethereum Proof-of-Authority (PoA) ブロックチェーン ネットワークであることが必要です。
    * このネットワーク上で、エンドポイントにパブリックにアクセスできることが必要です。
    * PoA ブロックチェーン ネットワークは、Gas (手数料) の設定額がゼロとなるように構成されている必要があります。

    > [!NOTE]
    > Blockchain Workbench の口座に資金が追加されることはありません。 資金が要求された場合、取引は失敗します。

    ![既存のブロック チェーン ネットワークの詳細設定](media/deploy/advanced-blockchain-settings-existing.png)

    | Setting | 説明  |
    |---------|--------------|
    | Ethereum RPC エンドポイント | 既存の PoA ブロックチェーン ネットワークの RPC エンドポイントを提供します。 エンドポイントは https:// または http:// で始まり、ポート番号で終わります。 たとえば、`http<s>://<network-url>:<port>` のように指定します。 |
    | Azure Active Directory の設定 | **[後で追加]** を選択します。</br>注:[Azure AD の事前構成](#azure-ad-configuration)を選択した場合、または再デプロイしている場合は、*[今すぐ追加]* を選択します。 |
    | VM の選択 | Blockchain ネットワークの望ましい VM サイズを選びます。 |

9. **[OK]** をクリックして [詳細設定] を完了します。

10. サマリーで、パラメーターが正しいことを確認します。

    ![まとめ](media/deploy/blockchain-workbench-summary.png)

11. **[作成]** を選び、条項に同意して、Azure Blockchain Workbench を展開します。

展開には、最大 90 分かかる場合があります。 Azure portal を使って進行状況を監視できます。 展開された成果物の状態を確認するには、新しく作成したリソース グループで **[デプロイ] > [概要]** を選びます。

> [!IMPORTANT]
> デプロイ後に、Active Directory の設定を完了する必要があります。 **[後で追加]** を選択した場合、[Azure AD 構成スクリプト](#azure-ad-configuration-script)を実行する必要があります。  **[今すぐ追加]** を選択した場合は、[応答 URL を構成](#configuring-the-reply-url)する必要があります。

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench の Web URL

Blockchain Workbench の展開が完了すると、新しいリソース グループには Blockchain Workbench のリソースが格納されています。 Blockchain Workbench サービスには、Web URL を使ってアクセスします。 次の手順では、展開済みのフレームワークの Web URL を取得する方法を示します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション ウィンドウで、**[リソース グループ]** を選びます。
3. Blockchain Workbench の展開時に指定したリソース グループ名を選びます。
4. **[種類]** 列見出しを選択して、種類のアルファベット順に一覧を並べ替えます。
5. **[App Service]** の種類で 2 つのリソースがあります。 "-api" サフィックスが "*付いていない*" リソースの種類 **[App Service]** を選びます。

    ![App Service のリスト](media/deploy/resource-group-list.png)

6. App Service の **[基本]** セクションで、**[URL]** の値をコピーします。これは、展開した Blockchain Workbench に対する Web URL を表します。

    ![App Service の基礎](media/deploy/app-service.png)

カスタム ドメイン名を Blockchain Workbench に関連付けるには、「[Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する](../../app-service/web-sites-traffic-manager-custom-domain-name.md)」を参照してください。

## <a name="azure-ad-configuration-script"></a>Azure AD 構成スクリプト

Blockchain Workbench のデプロイを完了するために Azure AD を構成する必要があります。 PowerShell スクリプトを使用して構成を行います。

1. ブラウザーで [Blockchain Workbench Web URL](#blockchain-workbench-web-url) に移動します。
2. Cloud Shell を使用して Azure AD を設定する手順が表示されます。 コマンドをコピーして、Cloud Shell を起動します。

    ![AAD スクリプトの起動](media/deploy/launch-aad-script.png)

3. Blockchain Workbench をデプロイした Azure AD テナントを選択します。
4. Cloud Shell で、コマンドを貼り付けて実行します。
5. 入力を求められたら、Blockchain Workbench のために使用する Azure AD テナントを入力します。 これが、Blockchain Workbench のユーザーを含むテナントになります。

    > [!IMPORTANT]
    > 認証されたユーザーは、Azure AD アプリケーション登録を作成し、委任されたアプリケーションのアクセス許可をテナント内で付与するためのアクセス許可が必要です。 場合によっては、Azure AD 構成スクリプトの実行または新しいテナントの作成を、テナントの管理者に依頼する必要があります。

    ![Azure AD テナントの入力](media/deploy/choose-tenant.png)

6. ブラウザーを使用し Azure AD テナントに対して認証するように求められます。 ブラウザーで Web URL を開き、コードを入力し、認証します。

    ![コードによる認証](media/deploy/authenticate.png)

7. スクリプトからいくつかのステータス メッセージが出力されます。 テナントが正常にプロビジョニングされると、**SUCCESS** ステータス メッセージを取得します。
8. Blockchain Workbench の URL に移動します。 ディレクトリに対する読み取りアクセス許可の付与に同意することを求められます。 これによって、Blockchain Workbench web アプリがテナント内のユーザーにアクセスできるようになります。 テナント管理者は、組織を代表して同意を選択できます。 このオプションは、テナント内のすべてのユーザーについて同意を受け入れます。 それ以外の場合、各ユーザーが Blockchain Workbench の Web アプリを初めて使用する際に同意を求められます。
9. **[許可]** を選択して同意します。

     ![ユーザー プロファイルの読み取りの同意](media/deploy/graph-permission-consent.png)

10. 同意した後で、Blockchain Workbench の Web アプリを使用できます。

## <a name="azure-ad-configuration"></a>Azure AD 構成

デプロイ前に Azure AD 設定の手動構成または確認を選択した場合、このセクションの手順をすべて完了してください。 Azure AD の設定を自動的に構成する場合は、Blockchain Workbench をデプロイした後で [Azure AD 構成スクリプト](#azure-ad-configuration-script)を使用します。

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API アプリの登録

Blockchain Workbench の展開には、Azure AD アプリケーションの登録が必要です。 アプリを登録するには Azure Active Directory (Azure AD) テナントが必要です。 既存のテナントを使うか、新しいテナントを作成することができます。 既存の Azure AD テナントを使う場合は、Azure AD テナントにアプリケーションを登録し、Graph API のアクセス許可を付与し、Azure AD テナント内でゲストのアクセスを許可するための十分なアクセス許可が必要です。 既存の Azure AD テナントに十分なアクセス許可がない場合は、新しいテナントを作成します。


1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 右上隅でお使いのアカウントを選び、目的の Azure AD テナントに切り替えます。 テナントは、Workbench を展開するサブスクリプションのサブスクリプション管理者のテナントでなければならず、アプリケーションを登録するための十分なアクセス許可が必要です。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。 **[アプリの登録]** > **[新しいアプリケーションの登録]** の順に選びます。

    ![アプリの登録](media/deploy/app-registration.png)

4. アプリケーションの **[名前]** と **[サインオン URL]** を指定します。 展開の間に値が変更されるため、プレースホルダーの値を使用できます。 

    ![アプリの登録を作成する](media/deploy/app-registration-create.png)

    |Setting  | 値  |
    |---------|---------|
    |Name | `Blockchain API` |
    |アプリケーションの種類 |Web アプリ/API|
    |[サインオン URL] | `https://blockchainapi` |

5. **[作成]** を選んで、Azure AD アプリケーションを登録します。

### <a name="modify-manifest"></a>マニフェストを変更する

次に、Azure AD 内のアプリケーション ロールを使って Blockchain Workbench 管理者を指定するように、マニフェストを変更する必要があります。  アプリケーション マニフェストについて詳しくは、「[Azure Active Directory アプリケーション マニフェスト](../../active-directory/develop/reference-app-manifest.md)」をご覧ください。

1. 登録したアプリケーションの詳細ウィンドウで **[マニフェスト]** を選びます。
2. GUID を生成します。 PowerShell コマンド [guid] :: NewGuid () または New-GUID コマンドレットを使用して、GUID を生成することができます。 GUID ジェネレーター Web サイトを使用することもできます。
3. マニフェストの **appRoles** セクションを更新します。 [マニフェストの編集] ウィンドウで、**[編集]** を選び、`"appRoles": []` を提供された JSON に置き換えます。 忘れずに、**id** フィールドの値を、生成した GUID に置き換えてください。 

    ![マニフェストの編集](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > 値 **Administrator** は、Blockchain Workbench の管理者を識別するために必要です。

4. マニフェスト内でも **Oauth2AllowImplictFlow** 値を **true** に変更します。

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. **[保存]** を選択してマニフェストの変更を保存します。

### <a name="add-graph-api-required-permissions"></a>Graph API で必要なアクセス許可を追加する

API アプリケーションは、ディレクトリにアクセスするために、ユーザーからのアクセス許可を要求する必要があります。 API アプリケーションに必要な次のアクセス許可を設定します。

1. Blockchain API アプリの登録で、**[設定] > [必要なアクセス許可] > [API を選択します] > [Microsoft Graph]** の順に選択します。

    ![API を選択する](media/deploy/client-app-select-api.png)

    **[選択]** をクリックします。

2. **[委任されたアクセス許可]** の **[アクセスの有効化]** で、**[すべてのユーザーの基本プロファイルの読み取り]** を選びます。

    ![アクセスを有効化](media/deploy/client-app-read-perms.png)

    **[保存]** を選択し、**[完了]** を選択します。

3. **[必要なアクセス許可]** で **[アクセス許可の付与]** を選び、確認の要求に **[はい]** を選びます。

   ![アクセス許可を付与する](media/deploy/client-app-grant-permissions.png)

   アクセス許可を付与することで、Blockchain Workbench はディレクトリのユーザーにアクセスできます。 メンバーを検索して Blockchain Workbench に追加するには、読み取りアクセス許可が必要です。

### <a name="get-application-id"></a>アプリケーション ID を取得する

展開には、アプリケーション ID とテナントの情報が必要です。 展開の間に使用できるように、情報を収集して保存します。

1. 登録したアプリケーションで、**[設定]** > **[プロパティ]** の順に選択します。
2. **[プロパティ]** ウィンドウで、次の値をコピーし、後で展開のときに使用できるように保存しておきます。

    ![API アプリのプロパティ](media/deploy/app-properties.png)

    | 保存する設定  | 展開での使用 |
    |------------------|-------------------|
    | アプリケーション ID | [Azure Active Directory setup]\(Azure Active Directory のセットアップ\) > [アプリケーション ID] |

### <a name="get-tenant-domain-name"></a>テナントのドメイン名を取得する

アプリケーションを登録する Active Directory テナントのドメイン名を収集して保存します。 

左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。 **[カスタム ドメイン名]** を選択します。 ドメイン名をコピーして保存します。

![ドメイン名](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>ゲスト ユーザーの設定

自分の Azure AD テナントにゲスト ユーザーが存在する場合は、追加の手順に従って、Blockchain Workbench のユーザー割り当ておよび管理が適切に作動していることを確認します。

1. 自分の Azure AD テナントに切り替えて、**[Azure Active Directory] > [ユーザー設定] > [外部コラボレーションの設定を管理します]** を選択します。
2. **[Guest user permissions are limited]\(ゲスト ユーザーのアクセス許可を制限する\)** を **[いいえ]** に設定します。
    ![外部コラボレーションの設定](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>応答 URL の構成

Azure Blockchain Workbench をデプロイした後は、デプロイした Blockchain Workbench Web URL について Azure Active Directory (Azure AD) クライアント アプリケーションの**応答 URL** を構成する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure AD クライアント アプリケーションを登録したテナントにいることを確認します。
3. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** サービスを選びます。 **[アプリの登録]** を選択します。
4. 前提条件セクションで登録した Azure AD クライアント アプリケーションを選びます。
5. **[設定] > [応答 URL]** を選びます。
6. 「**Blockchain Workbench の Web URL**」セクションで取得した Azure Blockchain Workbench の展開のメイン Web URL を指定します。 応答 URL には、プレフィックス `https://` が付加されます。 たとえば、`https://myblockchain2-7v75.azurewebsites.net` のように指定します。

    ![応答 URL](media/deploy/configure-reply-url.png)

7. **[保存]** を選んで、クライアントの登録を更新します。

## <a name="remove-a-deployment"></a>デプロイの削除

デプロイが不要になった場合は、Blockchain Workbench リソース グループを削除してデプロイを削除できます。

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。 
2. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、**[削除]** を選択します。

    ![Delete resource group](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>次の手順

このハウツー記事では、Azure Blockchain Workbench をデプロイしました。 ブロックチェーン アプリケーションの作成方法については、次のハウツー記事に進みます。

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench でブロックチェーン アプリケーションを作成する](create-app.md)
