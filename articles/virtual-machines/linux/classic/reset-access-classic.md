---
title: CLI からの Linux VM のパスワードと SSH キーのリセット | Microsoft Docs
description: Azure コマンド ライン インターフェイス (CLI) から VMAccess 拡張機能を使用して、Linux VM のパスワードまたは SSH キーをリセットし、SSH 構成を修正し、ディスクの整合性チェックを実行する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: 3984c94770290ad29394a06b20f2dc64a980c4a8
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071512"
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>VMAccess 拡張機能を使用して、Linux VM のパスワードまたは SSH キーをリセットし、SSH 構成を修正し、ディスクの整合性チェックを実行する方法について説明します。
パスワードを忘れたため、Secure Shell (SSH) キーが正しくないため、または SSH 構成に問題があるために、Azure の Linux 仮想マシンに接続できない場合は、Azure CLI で VMAccessForLinux 拡張機能を使用して、パスワードまたは SSH キーのリセット、SSH 構成の修正、ディスクの整合性のチェックを行います。 

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)方法について説明します。

Azure CLI を使用すると、コマンド ライン インターフェイス (Bash、ターミナル、コマンド プロンプト) から **azure vm extension set** コマンドを使用してコマンドにアクセスできます。 拡張機能の詳しい使用方法については、 **azure help vm extension set** を実行します。

Azure CLI を使用すると、次のタスクを実行できます。

* [パスワードのリセット](#pwresetcli)
* [SSH キーのリセット](#sshkeyresetcli)
* [パスワードと SSH キーのリセット](#resetbothcli)
* [新しい sudo ユーザー アカウントの作成](#createnewsudocli)
* [SSH 構成のリセット](#sshconfigresetcli)
* [ユーザーの削除](#deletecli)
* [VMAccess 拡張機能の状態の表示](#statuscli)
* [追加されたディスクの整合性のチェック](#checkdisk)
* [Linux VM での追加されたディスクの修復](#repairdisk)

## <a name="prerequisites"></a>前提条件
次の手順を実行する必要があります。

* [Azure CLI をインストール](../../../cli-install-nodejs.md)して、ログオンし、アカウントに関連付けられている Azure のリソースを使用する[サブスクリプションに接続](/cli/azure/authenticate-azure-cli)する必要があります。
* コマンド プロンプトで以下の内容を入力して、クラシック デプロイ モデルの正しいモードを設定します。
    ``` 
        azure config mode asm
    ```
* 新しいパスワードまたは一連の SSH キー (いずれかをリセットする場合)。 SSH の構成をリセットする場合、これらは必要ありません。

## <a name="pwresetcli"></a>パスワードのリセット
1. ローカル コンピューターで、次の内容を含む PrivateConf.json という名前のファイルを作成します。 **myUserName** と **myP@ssW0rd** を実際のユーザー名とパスワードに置き換え、独自の有効期限の日付を設定します。

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. **myVM** を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>SSH キーのリセット
1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。 **myUserName** と **mySSHKey** の値を実際の情報に置き換えます。

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. **myVM** を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>パスワードと SSH キーのリセット
1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。 **myUserName**、**mySSHKey**、**myP@ssW0rd** の値を実際の情報に置き換えます。

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. **myVM** を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>新しい sudo ユーザー アカウントの作成

自身のユーザー名を忘れた場合は、VMAccess を使用して、sudo 権限を持つ新しいユーザーを作成できます。 この場合、既存のユーザー名とパスワードは変更されません。

パスワードを使用したアクセス権を持つ新しい sudo ユーザーを作成するには、「 [パスワードのリセット](#pwresetcli) 」のスクリプトを使用して、新しいユーザー名を指定します。

SSH キーを使用したアクセス権を持つ新しい sudo ユーザーを作成するには、「 [SSH キーのリセット](#sshkeyresetcli) 」のスクリプトを使用して、新しいユーザー名を指定します。

[パスワードと SSH キーをリセット](#resetbothcli) して、パスワードと SSH キーの両方を使用したアクセス権を持つ新しいユーザーを作成することもできます。

## <a name="sshconfigresetcli"></a>SSH 構成のリセット
SSH の構成が望ましい状態でない場合は、VM にアクセスできなくなる可能性もあります。 VMAccess 拡張機能を使用して、構成を既定の状態にリセットすることができます。 そのために必要なのは、"reset_ssh" キーを "True" に設定することだけです。 拡張機能によって SSH サーバーが再起動し、VM 上の SSH ポートが開いて、SSH 構成が既定値にリセットされます。 ユーザー アカウント (名前、パスワード、または SSH キー) は変更されません。

> [!NOTE]
> リセットされる SSH の構成ファイルは、/etc/ssh/sshd_config にあります。
> 
> 

1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. **myVM** を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>ユーザーの削除
VM にログインせずに直接ユーザー アカウントを削除するには、このスクリプトを使用できます。

1. **removeUserName** を削除するユーザー名に置き換えて、次の内容を含む PrivateConf.json という名前のファイルを作成します。 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. **myVM** を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>VMAccess 拡張機能の状態の表示
VMAccess 拡張機能の状態を表示するには、次のコマンドを実行します。

```
        azure vm extension get
```

## <a name='checkdisk'></a>追加されたディスクの整合性のチェック
Linux 仮想マシンのすべてのディスクに対して fsck を実行するには、次の手順を実行する必要があります。

1. 次の内容を含む PublicConf.json という名前のファイルを作成します。 チェック ディスクは、仮想マシンに接続されているディスクをチェックするかどうかを表すブール値を受け取ります。 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. **myVM** を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>ディスクの修復
マウントされていないディスクまたはマウント構成エラーが発生したディスクを修復するには、VMAccess 拡張機能を使用して Linux 仮想マシンでマウント構成をリセットします。 **myDisk** を実際のディスクの名前に置き換えます。

1. 次の内容を含む PublicConf.json という名前のファイルを作成します。 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. **myVM** を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>次の手順
* Azure PowerShell コマンドレットまたは Azure Resource Manager テンプレートを使用して、パスワードまたは SSH キーをリセットし、SSH 構成を修正してディスクの整合性のチェックを行う場合、GitHub の [VMAccess 拡張機能に関するドキュメント](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)を参照してください。 
* クラシック デプロイ モデルでデプロイされた Linux VM のパスワードまたは SSH キーをリセットする場合は、[Azure ポータル](https://portal.azure.com) を使用することもできます。 Resource Manager デプロイ モデルでデプロイされた Linux VM のパスワードまたは SSH キーをリセットする場合は、現在ポータルを使用することができません。
* Azure 仮想マシンに VM 拡張機能を使用する方法の詳細については、「[仮想マシンの拡張機能とその機能について](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

