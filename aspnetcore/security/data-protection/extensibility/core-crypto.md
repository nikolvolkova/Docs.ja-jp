---
title: "コア暗号化の拡張性"
author: rick-anderson
description: "IAuthenticatedEncryptor、IAuthenticatedEncryptorDescriptor、IAuthenticatedEncryptorDescriptorDeserializer、および最上位のファクトリについて説明します。"
keywords: "ASP.NET Core、IAuthenticatedEncryptor、IAuthenticatedEncryptorDescriptor、IAuthenticatedEncryptorDescriptorDeserializer"
ms.author: riande
manager: wpickett
ms.date: 8/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 8ee4e380b154db7f1736edc793b56258655ddd52
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2017
---
# <a name="core-cryptography-extensibility"></a><span data-ttu-id="a212a-104">コア暗号化の拡張性</span><span class="sxs-lookup"><span data-stu-id="a212a-104">Core cryptography extensibility</span></span>

<a name=data-protection-extensibility-core-crypto></a>

>[!WARNING]
> <span data-ttu-id="a212a-105">次のインターフェイスのいずれかを実装する型はスレッド セーフである必要があります複数の呼び出し元のです。</span><span class="sxs-lookup"><span data-stu-id="a212a-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptor></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="a212a-106">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a212a-106">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a212a-107">**IAuthenticatedEncryptor**インターフェイスは、暗号化サブシステムの基本的なビルド ブロックです。</span><span class="sxs-lookup"><span data-stu-id="a212a-107">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="a212a-108">一般には、キー、ごとの 1 つの IAuthenticatedEncryptor と IAuthenticatedEncryptor インスタンスは、すべての暗号化キー マテリアルと暗号化操作の実行に必要なアルゴリズムの情報をラップします。</span><span class="sxs-lookup"><span data-stu-id="a212a-108">There is generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="a212a-109">その名前からわかるように、型、認証済みの暗号化と復号化サービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a212a-109">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="a212a-110">これは、次の 2 つの Api を公開します。</span><span class="sxs-lookup"><span data-stu-id="a212a-110">It exposes the following two APIs.</span></span>

* <span data-ttu-id="a212a-111">復号化 (ArraySegment<byte>暗号文、ArraySegment<byte> additionalAuthenticatedData): byte[]</span><span class="sxs-lookup"><span data-stu-id="a212a-111">Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

* <span data-ttu-id="a212a-112">暗号化 (ArraySegment<byte>プレーン テキスト、ArraySegment<byte> additionalAuthenticatedData): byte[]</span><span class="sxs-lookup"><span data-stu-id="a212a-112">Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

<span data-ttu-id="a212a-113">暗号化メソッドは、enciphered プレーン テキストとな認証タグを含む blob を返します。</span><span class="sxs-lookup"><span data-stu-id="a212a-113">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="a212a-114">認証タグは、AAD 自体は、最終的なペイロードから回復可能な必要はありませんが、追加の認証済みデータ (AAD) を囲んでいる必要があります。</span><span class="sxs-lookup"><span data-stu-id="a212a-114">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="a212a-115">復号化メソッドは、認証タグを検証し、解読はペイロードを返します。</span><span class="sxs-lookup"><span data-stu-id="a212a-115">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="a212a-116">すべてのエラー (ArgumentNullException を除くと似ています) は、CryptographicException に homogenized 必要があります。</span><span class="sxs-lookup"><span data-stu-id="a212a-116">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="a212a-117">IAuthenticatedEncryptor インスタンス自体実際には、キー マテリアルを格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a212a-117">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="a212a-118">たとえば、実装では、すべての操作の HSM に委任します。</span><span class="sxs-lookup"><span data-stu-id="a212a-118">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory></a>
<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="a212a-119">IAuthenticatedEncryptor を作成する方法</span><span class="sxs-lookup"><span data-stu-id="a212a-119">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a212a-120">ASP.NET 2.x のコア</span><span class="sxs-lookup"><span data-stu-id="a212a-120">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a212a-121">**IAuthenticatedEncryptorFactory**インターフェイスを作成する方法を認識する型を表す、 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="a212a-121">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a212a-122">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a212a-122">Its API is as follows.</span></span>

* <span data-ttu-id="a212a-123">CreateEncryptorInstance (IKey キー): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a212a-123">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a212a-124">指定された IKey インスタンスに対してその CreateEncryptorInstance メソッドによって作成された任意の認証済み受け付け必要があります等価と見なされる、ように、次のコード例です。</span><span class="sxs-lookup"><span data-stu-id="a212a-124">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a212a-125">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a212a-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a212a-126">**IAuthenticatedEncryptorDescriptor**インターフェイスを作成する方法を認識する型を表す、 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="a212a-126">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a212a-127">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a212a-127">Its API is as follows.</span></span>

* <span data-ttu-id="a212a-128">CreateEncryptorInstance(): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a212a-128">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="a212a-129">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a212a-129">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="a212a-130">IAuthenticatedEncryptor と同様に IAuthenticatedEncryptorDescriptor のインスタンスが 1 つの特定のキーをラップすると見なされます。</span><span class="sxs-lookup"><span data-stu-id="a212a-130">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="a212a-131">つまりが、特定の IAuthenticatedEncryptorDescriptor インスタンス、その CreateEncryptorInstance メソッドによって作成された任意の認証済み受け付け必要がありますと見なされるように、同等の次のコード例です。</span><span class="sxs-lookup"><span data-stu-id="a212a-131">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="a212a-132">IAuthenticatedEncryptorDescriptor (ASP.NET のコア 2.x のみ)</span><span class="sxs-lookup"><span data-stu-id="a212a-132">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a212a-133">ASP.NET 2.x のコア</span><span class="sxs-lookup"><span data-stu-id="a212a-133">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a212a-134">**IAuthenticatedEncryptorDescriptor**インターフェイスは、型自体を XML にエクスポートする方法を把握していることを表します。</span><span class="sxs-lookup"><span data-stu-id="a212a-134">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="a212a-135">その API は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a212a-135">Its API is as follows.</span></span>

* <span data-ttu-id="a212a-136">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a212a-136">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a212a-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a212a-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="a212a-138">XML シリアル化</span><span class="sxs-lookup"><span data-stu-id="a212a-138">XML Serialization</span></span>

<span data-ttu-id="a212a-139">IAuthenticatedEncryptor と IAuthenticatedEncryptorDescriptor の主な違いは、記述子が、暗号化機能を作成し、有効な引数で指定する方法を知っていることです。</span><span class="sxs-lookup"><span data-stu-id="a212a-139">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="a212a-140">これらの実装では、対称アルゴリズムおよび KeyedHashAlgorithm 依存、IAuthenticatedEncryptor を検討してください。</span><span class="sxs-lookup"><span data-stu-id="a212a-140">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="a212a-141">暗号化機能のジョブが、これらの型を使用するが、必ずしもを認識していないこれらの型が送信元アプリケーションが再起動した場合、それ自体を再作成する方法の適切な説明を本当に書き込むことができないようにします。</span><span class="sxs-lookup"><span data-stu-id="a212a-141">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="a212a-142">記述子は、さらに高いレベルとして機能します。</span><span class="sxs-lookup"><span data-stu-id="a212a-142">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="a212a-143">記述子は、暗号化機能のインスタンスを作成する方法を認識しているため (たとえば、認識必要なアルゴリズムを作成する方法)、アプリケーションをリセットした後、暗号化機能のインスタンスが再作成できるようには XML 形式でそのナレッジがシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="a212a-143">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name=data-protection-extensibility-core-crypto-exporttoxml></a>

<span data-ttu-id="a212a-144">記述子は、その ExportToXml ルーチンを使用してシリアル化できます。</span><span class="sxs-lookup"><span data-stu-id="a212a-144">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="a212a-145">このルーチンは、次の 2 つのプロパティが含まれて XmlSerializedDescriptorInfo を返します: 記述子とを表す型の XElement 形式、 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)があります指定された対応する XElement この記述子を再生するために使用します。</span><span class="sxs-lookup"><span data-stu-id="a212a-145">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="a212a-146">シリアル化された記述子には、暗号化キー マテリアルなどの機密情報を含めることがあります。</span><span class="sxs-lookup"><span data-stu-id="a212a-146">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="a212a-147">データ保護システムには、これが記憶域に保存される前に、情報を暗号化するための組み込みサポートがあります。</span><span class="sxs-lookup"><span data-stu-id="a212a-147">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="a212a-148">これを利用するには、記述子は (xmlns"http://schemas.asp.net/2015/03/dataProtection")、値"true"を属性名"requiresEncryption"で機密情報を含む要素をマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a212a-148">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="a212a-149">この属性を設定するヘルパー API があります。</span><span class="sxs-lookup"><span data-stu-id="a212a-149">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="a212a-150">XElement.MarkAsRequiresEncryption() が Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 名前空間にある拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a212a-150">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="a212a-151">ここで、シリアル化された記述子が含まれていない機密情報には場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a212a-151">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="a212a-152">HSM に格納されている暗号化キーの大文字と小文字を再度検討してください。</span><span class="sxs-lookup"><span data-stu-id="a212a-152">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="a212a-153">記述子は、HSM はプレーン テキスト形式の内容を公開しないために、自体シリアル化するとき、キー マテリアルを書き込むことはできません。</span><span class="sxs-lookup"><span data-stu-id="a212a-153">The descriptor cannot write out the key material when serializing itself since the HSM will not expose the material in plaintext form.</span></span> <span data-ttu-id="a212a-154">代わりに、記述子 (HSM は、この方法でエクスポートを許可する) 場合、キーまたはキーを HSM の一意識別子のキーによってラップされたバージョンが書き込まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a212a-154">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="a212a-155">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="a212a-155">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="a212a-156">**IAuthenticatedEncryptorDescriptorDeserializer**インターフェイスは XElement から IAuthenticatedEncryptorDescriptor インスタンスを逆シリアル化する方法を認識する型を表します。</span><span class="sxs-lookup"><span data-stu-id="a212a-156">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="a212a-157">1 つのメソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="a212a-157">It exposes a single method:</span></span>

* <span data-ttu-id="a212a-158">ImportFromXml (XElement 要素): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a212a-158">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a212a-159">ImportFromXml メソッドは、返された XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)され元 IAuthenticatedEncryptorDescriptor の同等なが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a212a-159">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="a212a-160">IAuthenticatedEncryptorDescriptorDeserializer を実装する型は、次の 2 つのパブリック コンス トラクターのいずれかが必要です。</span><span class="sxs-lookup"><span data-stu-id="a212a-160">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="a212a-161">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="a212a-161">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="a212a-162">.ctor()</span><span class="sxs-lookup"><span data-stu-id="a212a-162">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="a212a-163">コンス トラクターに渡される IServiceProvider を null にすることがあります。</span><span class="sxs-lookup"><span data-stu-id="a212a-163">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="a212a-164">最上位のファクトリ</span><span class="sxs-lookup"><span data-stu-id="a212a-164">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a212a-165">ASP.NET 2.x のコア</span><span class="sxs-lookup"><span data-stu-id="a212a-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a212a-166">**AlgorithmConfiguration**クラスを作成する方法を知っている型を表す[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="a212a-166">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a212a-167">これは、単一の API を公開します。</span><span class="sxs-lookup"><span data-stu-id="a212a-167">It exposes a single API.</span></span>

* <span data-ttu-id="a212a-168">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a212a-168">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a212a-169">AlgorithmConfiguration の最上位のファクトリとして考えます。</span><span class="sxs-lookup"><span data-stu-id="a212a-169">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="a212a-170">構成は、テンプレートとして機能します。</span><span class="sxs-lookup"><span data-stu-id="a212a-170">The configuration serves as a template.</span></span> <span data-ttu-id="a212a-171">アルゴリズムの情報をラップ (例: この構成によって生成される値、AES 128-GCM マスター _ キーを持つ記述子) がまだ特定のキーでに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="a212a-171">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="a212a-172">CreateNewDescriptor し、呼ばれ、この呼び出しのためだけに最新のキー マテリアルが作成された新しい IAuthenticatedEncryptorDescriptor が生成されるときに、このキー マテリアル、および、マテリアルを使用する必要な情報をアルゴリズムをラップします。</span><span class="sxs-lookup"><span data-stu-id="a212a-172">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a212a-173">キー マテリアルでしたするソフトウェアで作成された (メモリ内に保持) と作成され、HSM およびよびな内で保持されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a212a-173">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a212a-174">重要な点は、CreateNewDescriptor への呼び出しの 2 つが等価 IAuthenticatedEncryptorDescriptor インスタンスを作成しないでことです。</span><span class="sxs-lookup"><span data-stu-id="a212a-174">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a212a-175">AlgorithmConfiguration 型ポイントとして機能、エントリのキーの作成ルーチンなど[自動キーのローリング](../implementation/key-management.md#data-protection-implementation-key-management)です。</span><span class="sxs-lookup"><span data-stu-id="a212a-175">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="a212a-176">将来のすべてのキーの実装を変更するには、KeyManagementOptions で AuthenticatedEncryptorConfiguration プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="a212a-176">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a212a-177">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a212a-177">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a212a-178">**IAuthenticatedEncryptorConfiguration**インターフェイスを作成する方法を知っている型を表す[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)インスタンス。</span><span class="sxs-lookup"><span data-stu-id="a212a-178">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a212a-179">これは、単一の API を公開します。</span><span class="sxs-lookup"><span data-stu-id="a212a-179">It exposes a single API.</span></span>

* <span data-ttu-id="a212a-180">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a212a-180">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a212a-181">IAuthenticatedEncryptorConfiguration の最上位のファクトリとして考えます。</span><span class="sxs-lookup"><span data-stu-id="a212a-181">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="a212a-182">構成は、テンプレートとして機能します。</span><span class="sxs-lookup"><span data-stu-id="a212a-182">The configuration serves as a template.</span></span> <span data-ttu-id="a212a-183">アルゴリズムの情報をラップ (例: この構成によって生成される値、AES 128-GCM マスター _ キーを持つ記述子) がまだ特定のキーでに関連付けられていません。</span><span class="sxs-lookup"><span data-stu-id="a212a-183">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="a212a-184">CreateNewDescriptor し、呼ばれ、この呼び出しのためだけに最新のキー マテリアルが作成された新しい IAuthenticatedEncryptorDescriptor が生成されるときに、このキー マテリアル、および、マテリアルを使用する必要な情報をアルゴリズムをラップします。</span><span class="sxs-lookup"><span data-stu-id="a212a-184">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a212a-185">キー マテリアルでしたするソフトウェアで作成された (メモリ内に保持) と作成され、HSM およびよびな内で保持されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a212a-185">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a212a-186">重要な点は、CreateNewDescriptor への呼び出しの 2 つが等価 IAuthenticatedEncryptorDescriptor インスタンスを作成しないでことです。</span><span class="sxs-lookup"><span data-stu-id="a212a-186">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a212a-187">IAuthenticatedEncryptorConfiguration 型ポイントとして機能、エントリのキーの作成ルーチンなど[自動キーのローリング](../implementation/key-management.md#data-protection-implementation-key-management)です。</span><span class="sxs-lookup"><span data-stu-id="a212a-187">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="a212a-188">将来のすべてのキーの実装を変更するには、サービス コンテナーに IAuthenticatedEncryptorConfiguration シングルトンを登録します。</span><span class="sxs-lookup"><span data-stu-id="a212a-188">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---