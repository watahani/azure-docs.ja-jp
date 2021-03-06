---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201484"
---
昇格された特権で PowerShell コンソールを開きます。



Azure PowerShell をローカルで実行している場合は、お使いの Azure アカウントに接続してください。 *Connect-AzureRmAccount* コマンドレットを実行すると、資格情報の入力を求められます。 認証後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。 PowerShell をローカルで実行しているのではなく、ブラウザーで Azure Cloud Shell の "試してみる" を使用している場合は、この最初の手順はスキップしてください。 お使いの Azure アカウントに自動的に接続されます。

```azurepowershell
Connect-AzureRmAccount
```

複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧を取得します。

```azurepowershell-interactive
Get-AzureRmSubscription
```

使用するサブスクリプションを指定します。

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```