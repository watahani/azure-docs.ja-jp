---
title: チュートリアル:Azure Active Directory と Small Improvements の統合 | Microsoft Docs
description: Azure Active Directory と Small Improvements の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 588e56c9ae22578c08dbca07c7c576fe8b577b58
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012336"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>チュートリアル:Azure Active Directory と Small Improvements の統合

このチュートリアルでは、Small Improvements と Azure Active Directory (Azure AD) を統合する方法について説明します。

Small Improvements と Azure AD の統合には、次の利点があります。

- Small Improvements にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Small Improvements にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Small Improvements の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Small Improvements でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Small Improvements を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-small-improvements-from-the-gallery"></a>ギャラリーから Small Improvements を追加する
Azure AD への Small Improvements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Small Improvements を追加する必要があります。

**ギャラリーから Small Improvements を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Small Improvements**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

1. 結果パネルで **[Small Improvements]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Small Improvements で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Small Improvements ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Small Improvements の関連ユーザーの間で、リンク関係が確立されている必要があります。

Small Improvements で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Small Improvements で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Small Improvements テスト ユーザーの作成](#creating-a-small-improvements-test-user)** - Small Improvements で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Small Improvements アプリケーションでシングル サインオンを構成します。

**Small Improvements で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Small Improvements** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

1. **[Small Improvements のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.small-improvements.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.small-improvements.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Small Improvements クライアント サポート チーム](mailto:support@small-improvements.com)に連絡してください。 
 
1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_general_400.png)

1. **[Small Improvements Configuration] (Small Improvements 構成)** セクションで、**[Configure Small Improvements] (Small Improvements の構成)** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

1. 別の Web ブラウザー ウィンドウで、管理者として Small Improvements 企業サイトにサインオンします。

1. メイン ダッシュボード ページで、左側の **[管理]** ボタンをクリックします。
   
    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. **[統合]** セクションで、**[SAML SSO]** ボタンをクリックします。
   
    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. [SSO Setup] ページで、次の手順に従います。
   
    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. **[HTTP エンドポイント]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b. ダウンロードした証明書をメモ帳で開き、その内容をコピーして、**[x509 証明書]** ボックスに貼り付けます。 

    c. ユーザーが SSO およびログイン フォーム認証オプションを使用できるようにするには、**[Enable access via login/password too (ログイン/パスワードによるアクセスも有効にする)]** オプションをオンにします。  

    d. **[SAML プロンプト]** ボックスに、SSO ログイン ボタンの名前として適切な値を入力します。  

    e. **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-small-improvements-test-user"></a>Small Improvements テスト ユーザーの作成

Azure AD ユーザーが Small Improvements にログインできるようにするには、そのユーザーを Small Improvements にプロビジョニングする必要があります。 Small Improvements の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Small Improvements 企業サイトにサインオンします。

1. ホーム ページの左側のメニューに移動し、 **[管理]** をクリックします。

1. [ユーザー管理] セクションで、 **[ユーザー ディレクトリ]** ボタンをクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. **[ユーザーの追加]** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. **[ユーザーの追加]** ダイアログで、次の手順を実行します。 

    ![Azure AD のテスト ユーザーの作成](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. 「**Britta**」のように、ユーザーの **[名]** を入力します。

    b. 「**Simon**」のように、ユーザーの **[姓]** を入力します。

    c. <strong>brittasimon@contoso.com</strong> のように、ユーザーの **[メール]** を入力します。 

    d. **[通知電子メールを送信する]** ボックスに、個人のメッセージを入力することもできます。 通知を送信しない場合は、このチェックボックスをオフにします。

    e. **[ユーザーの作成]** をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Small Improvements へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Small Improvements に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、 **[Small Improvements]** を選択します。

    ![Configure single sign-on](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [Small Improvements] タイルをクリックすると、自動的に Small Improvements アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

