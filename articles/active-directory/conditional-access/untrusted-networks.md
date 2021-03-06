---
title: Azure Active Directory (Azure AD) の条件付きアクセスを使用して信頼されていないネットワークからのアクセスに対して多要素認証 (MFA) を必須にする方法 | Microsoft Docs
description: 信頼されていないネットワークからのアクセスの試行について、Azure Active Directory (Azure AD) で条件付きアクセス ポリシーを構成する方法を説明します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c40db6c253899d7aab21d277e93b23dd0c6feb97
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314008"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>方法:条件付きアクセスを使用して信頼されていないネットワークからのアクセスに対して MFA を必須にする   

Azure Active Directory (Azure AD) を使用すると、任意の場所からデバイス、アプリ、およびサービスにシングル サインオンできます。 ユーザーは組織のネットワークからだけでなく、信頼されていない任意のインターネットの場所からも、クラウド アプリにアクセスできるようになります。 信頼されていないネットワークからのアクセスに関する一般的なベスト プラクティスは、多要素認証 (MFA) を必須にすることです。

この記事では、信頼されていないネットワークからのアクセスに対して MFA を必須にする条件付きアクセス ポリシーを構成するために必要な情報を提供します。 

## <a name="prerequisites"></a>前提条件

この記事では、次の内容を熟知していることを前提としています。 

- Azure AD の条件付きアクセスの[基本的な概念](overview.md) 
- Azure portal における条件付きアクセス ポリシーの構成に関する[ベスト プラクティス](best-practices.md)



## <a name="scenario-description"></a>シナリオの説明

セキュリティと生産性のバランスを得るには、組織のネットワークからのサインインにパスワードを必須にするだけで十分な場合があります。 しかし、信頼されていないネットワークの場所からのアクセスに関しては、正当なユーザーによってサインインが行われないリスクが高まります。 この問題に対処するために、信頼されていないネットワークからのアクセスをブロックできます。 また、別の方法として、多要素認証 (MFA) を必須にして、アカウントの正当な所有者によって試行されたというさらなる保証を得ることもできます。 

Azure AD 条件付きアクセスを使用すると、アクセス許可を付与するための次に示すポリシーを 1 つ使って、この要件に対応できます。 

- 選択したクラウド アプリに対して

- 選択したユーザーおよびグループに対して  

- 多要素認証を必須にする 

- 次のアクセス元の場合: 

    - 信頼されていない場所


## <a name="implementation"></a>実装

このシナリオの課題は、*信頼されていないネットワークの場所からのアクセス*を、条件付きアクセスの条件に変換することです。 条件付きアクセス ポリシーでは、[場所の条件](location-condition.md)を構成して、ネットワークの場所に関連するシナリオに対応できます。 場所の条件を利用して、IP アドレスの範囲、国、およびリージョンの論理グループ化である名前付きの場所を選択できます。  

通常、組織では、1 つまたは複数アドレスの範囲 (たとえば、199.30.16.0 ～ 199.30.16.24) を保持しています。
名前付きの場所は、次の方法で構成できます。

- この範囲 (199.30.16.0 ～ 24) を指定する 

- **企業ネットワーク**など、わかりやすい名前を割り当てる 


信頼されていないすべての場所が何かの定義を試みる代わりに、次のことを実行できます。

- あらゆる場所を含める 

    ![条件付きアクセス](./media/untrusted-networks/02.png)

- すべての信頼できる場所を除外する 

    ![条件付きアクセス](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>ポリシーのデプロイ

この記事で説明した方法を使って、信頼されていない場所に対する条件付きアクセス ポリシーを構成できるようになりました。 ポリシーが期待どおりに機能することを確実にするために推奨されるベスト プラクティスは、運用環境にロールアウトする前にポリシーをテストすることです。 テスト テナントを使用して、新しいポリシーが意図したとおりに機能するかどうかを確認するのが理想的です。 詳細については、[新しいポリシーのデプロイ方法](best-practices.md#how-should-you-deploy-a-new-policy)に関するページを参照してください。 



## <a name="next-steps"></a>次の手順

条件付きアクセスの詳細については、「[Azure Active Directory の条件付きアクセスの概要](../active-directory-conditional-access-azure-portal.md)」を参照してください。