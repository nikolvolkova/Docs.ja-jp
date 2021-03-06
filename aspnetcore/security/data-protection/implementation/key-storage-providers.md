---
title: "キー記憶域プロバイダー"
author: rick-anderson
description: "キー記憶域プロバイダー"
keywords: "encryption,ASP.NET コア"
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 423e0a79-2f34-44c4-aaf3-146a53c39251
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 952fc99d2486b8e78d97356c333257e581a79b42
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2017
---
# <a name="key-storage-providers"></a>キー記憶域プロバイダー

<a name="data-protection-implementation-key-storage-providers"></a>

既定では、データ保護システム[ヒューリスティックを使用して](../configuration/default-settings.md#data-protection-default-settings)を暗号化キー マテリアルを保持するかを判断します。 開発者は、ヒューリスティックをオーバーライドし、手動で、場所を指定します。

> [!NOTE]
> キーの永続化を明示的な場所を指定する場合、データ保護システムはの登録を解除ヒューリスティックが提供する rest メカニズムで既定のキーの暗号化キーは、残りの部分では暗号化されなくなりますようにします。 お勧めするさらに[明示的なキーの暗号化メカニズムが指定](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers)実可動アプリケーション用。

データ保護システムは、いくつかのボックス内のキー記憶域プロバイダーが付属します。

## <a name="file-system"></a>ファイル システム

多くのアプリがファイル システムに基づくキー リポジトリを使用すると予測されます。 これを構成するのには、呼び出し、 [PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs)次に示すように構成ルーチンです。 提供、`DirectoryInfo`キーを格納するリポジトリをポイントします。

```csharp
sc.AddDataProtection()
       // persist keys to a specific directory
       .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
   ```

`DirectoryInfo`ローカル コンピューター上のディレクトリを指すことができます、またはネットワーク共有上のフォルダーを指すことができます。 ローカル コンピューター上のディレクトリを指している場合 (およびシナリオは、ローカル コンピューター上のアプリケーションのみ このリポジトリを使用する必要があります)、使用を検討して[Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)静止したキーを暗号化します。 それ以外の場合の使用を検討、 [X.509 証明書](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)静止したキーを暗号化します。

## <a name="azure-and-redis"></a>Azure と Redis

`Microsoft.AspNetCore.DataProtection.AzureStorage`と`Microsoft.AspNetCore.DataProtection.Redis`パッケージは、Azure Storage または Redis cache で、データ保護キーの保存を許可します。 キーは、web アプリの複数のインスタンスで共有することができます。 ASP.NET Core アプリでは、複数のサーバー認証 cookie または CSRF protection を共有できます。 Azure の構成する場合のいずれかを呼び出して、 [PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs)次に示すようにオーバー ロードします。

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));

    services.AddMvc();
}
```

参照、 [Azure テスト コード](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs)です。

Redis の構成する場合にのいずれかを呼び出して、 [PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs)次に示すようにオーバー ロードします。

```
public void ConfigureServices(IServiceCollection services)
{
    // Connect to Redis database.
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");

    services.AddMvc();
}
```

詳しくは、次のトピックをご覧ください。

- [StackExchange.Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
- [Azure Redis キャッシュ](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
- [テスト コードを redis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs)です。

## <a name="registry"></a>レジストリ

場合によって、アプリでは、ファイル システムへの書き込みアクセスがないこと。 (W3wp.exe のアプリケーション プール id) などの仮想サービス アカウントとして、アプリが実行されているシナリオを検討してください。 このような場合は、管理者をレジストリ キーは、サービス アカウントの資格情報の適切な ACLed をプロビジョニングしている可能性があります。 呼び出す、 [PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs)次に示すように構成ルーチンです。 提供、`RegistryKey`キー/値の暗号化を格納する場所を指しています。

```csharp
   sc.AddDataProtection()
       // persist keys to a specific location in the system registry
       .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
   ```

永続化メカニズムとして、システム レジストリを使用する場合は、使用を検討して[Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)静止したキーを暗号化します。

## <a name="custom-key-repository"></a>カスタム キー リポジトリ

カスタムを提供することによって、開発者が独自のキーの永続化メカニズムを指定するインボックス メカニズムが適切でない場合`IXmlRepository`です。
