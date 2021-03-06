---
title: "ASP.NET と ASP.NET Core の選択"
author: rick-anderson
description: "ASP.NET と ASP.NET Core のどちらかを選択する方法について説明します。"
keywords: "ASP.NET Core,基礎,概要"
ms.author: riande
manager: wpickett
ms.date: 09/30/2017
ms.topic: article
ms.assetid: f0d17abf-3c69-413e-87fc-30780805e33f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 875064bd3437acc4e2a53220e19e86431d8c159b
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2017
---
# <a name="choose-between-aspnet-and-aspnet-core"></a>ASP.NET と ASP.NET Core の選択 

Windows Server を対象とするエンタープライズ Web アプリケーションから、Linux コンテナーを対象とする小さなマイクロサービスまで、どのような Web アプリケーションを作成している場合でも、ASP.NET はあらゆるソリューションを提供します。

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core は、Windows、macOS、または Linux で最新のクラウド ベースの Web アプリケーションを構築するための、オープン ソースのクロスプラットフォーム フレームワークです。

## <a name="aspnet"></a>ASP.NET

ASP.NET は成熟したフレームワークであり、Windows でエンタープライズ クラスのサーバー ベース Web アプリケーションを構築するために必要なすべてのサービスを提供します。

## <a name="which-one-is-right-for-me"></a>どちらが適していますか?

| ASP.NET Core | ASP.NET |
|---|---|
|Windows、macOS、Linux が対象|Windows が対象|
|[Razor ページ](xref:mvc/razor-pages/index)は、ASP.NET Core 2.0 で Web UI を作成するための推奨される方法です。 [MVC](xref:mvc/overview) および [Web API](xref:tutorials/first-web-api) もご覧ください|[Web フォーム](https://docs.microsoft.com/aspnet/web-forms)、[SignalR](https://docs.microsoft.com/aspnet/signalr)、[MVC](https://docs.microsoft.com/aspnet/mvc)、[Web API](https://docs.microsoft.com/aspnet/web-api/)、または [Web ページ](https://docs.microsoft.com/aspnet/web-pages)を使います|
|コンピューターごとに複数のバージョン|コンピューターごとに 1 つのバージョン|
|Visual Studio、[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)、または [Visual Studio Code](https://code.visualstudio.com/) で C# または F# を使って開発します|Visual Studio で C#、VB、または F# を使って開発します|
|ASP.NET より高いパフォーマンス|よいパフォーマンス|
|[.NET Framework または .NET Core ランタイムを選択します](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)|.NET Framework ランタイムを使います|

## <a name="aspnet-core-scenarios"></a>ASP.NET Core のシナリオ

<!-- update link to Razor Pages mvc movie series when done -->
* [Razor ページ](xref:mvc/razor-pages/index)は、ASP.NET Core 2.0 で Web UI を作成するための推奨される方法です。
* [Web サイト](xref:tutorials/first-mvc-app/index)
* [API](xref:tutorials/first-web-api)

## <a name="aspnet-scenarios"></a>ASP.NET のシナリオ

* [Web サイト](https://docs.microsoft.com/aspnet/mvc)
* [API](https://docs.microsoft.com/aspnet/web-api)
* [リアルタイム](https://docs.microsoft.com/aspnet/signalr)

## <a name="resources"></a>リソース

* [ASP.NET の概要](https://docs.microsoft.com/aspnet/overview)
* [ASP.NET Core の概要](xref:index)
