---
title: HTTP API を使用した gRPC サービスの比較
author: jamesnk
description: GRPC と HTTP Api との比較、および推奨されるシナリオについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/31/2019
uid: grpc/comparison
ms.openlocfilehash: c34c7ecb668e478e2be3271928a2439979a746d9
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310471"
---
# <a name="comparing-grpc-services-with-http-apis"></a>HTTP API を使用した gRPC サービスの比較

[James のニュートン-キング](https://twitter.com/jamesnk)別

この記事では、 [Grpc サービス](https://grpc.io/docs/guides/)と HTTP api (ASP.NET Core [Web api](xref:web-api/index)を含む) の比較について説明します。 アプリの API を提供するために使用されるテクノロジは重要な選択であり、gRPC は HTTP Api と比較して独自の利点を提供します。 この記事では、gRPC の長所と短所について説明し、他のテクノロジで gRPC を使用する場合のシナリオを推奨します。

#### <a name="overview"></a>概要

| 機能          | gRPC                                               | HTTP Api と JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| コントラクト         | 必須 (*プロトコル*)                                | 省略可能 (OpenAPI)            |
| Transport        | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (小、バイナリ)](#performance)           | JSON (大規模で人間が読みやすい)  |
| Prescriptiveness | [厳密な指定](#strict-specification)      | ペイント. HTTP はすべて有効です      |
| ストリーム        | [クライアント、サーバー、双方向](#streaming)       | クライアント、サーバー                |
| ブラウザー サポート  | [いいえ (grpc-web が必要)](#limited-browser-support) | [はい]                           |
| セキュリティ         | トランスポート (HTTPS)                                  | トランスポート (HTTPS)             |
| クライアントコード生成  | [はい](#code-generation)                            | OpenAPI + サードパーティ製ツール |

## <a name="grpc-strengths"></a>gRPC の長所

### <a name="performance"></a>パフォーマンス

gRPC メッセージは、効率的なバイナリメッセージ形式である[Protobuf](https://developers.google.com/protocol-buffers/docs/overview)を使用してシリアル化されます。 Protobuf は、サーバーとクライアント上で非常に高速にシリアル化します。 Protobuf のシリアル化では、モバイルアプリのような限られた帯域幅シナリオで重要なメッセージペイロードが小さくなります。

gRPC は http/2 向けに設計されており、http 1.x に比べてパフォーマンスが大幅に向上します。

* バイナリフレームと圧縮。 HTTP/2 プロトコルは、送信と受信の両方においてコンパクトで効率的です。
* 1つの TCP 接続での複数の HTTP/2 呼び出しの多重化。 多重化[により、行のブロック](https://en.wikipedia.org/wiki/Head-of-line_blocking)が不要になります。

### <a name="code-generation"></a>コード生成

すべての gRPC フレームワークは、コード生成のためのファーストクラスのサポートを提供します。 GRPC 開発の中核となるファイルは、gRPC サービスとメッセージのコントラクトを定義する[*プロトコル*ファイル](https://developers.google.com/protocol-buffers/docs/proto3)です。 このファイルから、gRPC フレームワークによって、サービスの基本クラス、メッセージ、および完全なクライアントがコードによって生成されます。

サーバーとクライアント`*.proto`の間でファイルを共有することにより、メッセージとクライアントコードをエンドツーエンドで生成できます。 クライアントのコード生成によって、クライアントとサーバー上のメッセージの重複が排除され、厳密に型指定されたクライアントが作成されます。 クライアントを作成する必要がない場合は、多くのサービスを持つアプリケーションで大幅な開発時間を節約できます。

### <a name="strict-specification"></a>厳密な指定

JSON での HTTP API の正式な仕様は存在しません。 開発者は、Url、HTTP 動詞、および応答コードの最適な形式について議論します。

[Grpc 仕様](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)は、grpc サービスが従う必要がある形式について規範としています。 gRPC は、gPRC がプラットフォームと実装全体で一貫しているため、開発時間を短縮し、開発者の時間を節約します。

### <a name="streaming"></a>ストリーム

HTTP/2 は、有効期間が長いリアルタイム通信ストリームの基盤を提供します。 gRPC では、HTTP/2 を使用したストリーミングがファーストクラスでサポートされています。

GRPC サービスは、ストリーミングのすべての組み合わせをサポートしています。

* 単項 (ストリーミングなし)
* サーバーからクライアントへのストリーミング
* クライアントからサーバーへのストリーミング
* 双方向ストリーミング

### <a name="deadlinetimeouts-and-cancellation"></a>期限/タイムアウトとキャンセル

gRPC を使用すると、クライアントが RPC の完了を待機する時間を指定できます。 [期限](https://grpc.io/blog/deadlines)はサーバーに送信され、サーバーは期限を超えた場合に実行するアクションを決定できます。 たとえば、サーバーは、タイムアウト時に、実行中の gRPC/HTTP/データベース要求をキャンセルする場合があります。

子 gRPC 呼び出しによって期限と取り消しを反映すると、リソース使用量の制限を適用できます。

## <a name="grpc-recommended-scenarios"></a>gRPC の推奨されるシナリオ

gRPC は、次のシナリオに適しています。

* **マイクロサービス**&ndash; grpc は、待機時間が短く、スループットの高い通信を実現するように設計されています。 gRPC は、効率性が非常に重要な軽量マイクロサービスに適しています。
* **ポイントツーポイントのリアルタイム通信**&ndash; grpc は双方向ストリーミングに対して優れたサポートをしています。 gRPC サービスは、ポーリングせずにリアルタイムでメッセージをプッシュできます。
* **多言語環境**&ndash; grpc ツールは、一般的なすべての開発言語をサポートしています。 grpc は、多言語環境に適しています。
* **ネットワークの制約付き環境**&ndash; grpc メッセージは、ライトウェイトメッセージ形式である Protobuf を使用してシリアル化されます。 GRPC メッセージは、常に同等の JSON メッセージよりも小さくなります。

## <a name="grpc-weaknesses"></a>gRPC の短所

### <a name="limited-browser-support"></a>制限付きブラウザーサポート

現在、ブラウザーから gRPC サービスを直接呼び出すことはできません。 gRPC は HTTP/2 機能を多用しており、gRPC クライアントをサポートするために web 要求で必要な制御レベルを提供するブラウザーはありません。 たとえば、ブラウザーでは、呼び出し元が HTTP/2 を使用するように要求したり、基になる HTTP/2 フレームにアクセスしたりすることを許可していません。

[grpc-Web](https://grpc.io/docs/tutorials/basic/web.html)は、ブラウザーで制限付きの grpc サポートを提供する grpc チームの追加テクノロジです。 gRPC-Web は、すべての最新のブラウザーをサポートする JavaScript クライアントと、サーバー上の gRPC-Web プロキシの2つの部分で構成されています。 GRPC-Web クライアントはプロキシを呼び出し、プロキシは gRPC の要求を gRPC サーバーに転送します。

Grpc のすべての機能が gRPC-Web でサポートされているわけではありません。 クライアントと双方向のストリーミングはサポートされておらず、サーバーストリーミングのサポートは限られています。

### <a name="not-human-readable"></a>人間が判読できない

HTTP API 要求はテキストとして送信され、人間が読み取りや作成を行うことができます。

gRPC メッセージは、既定で Protobuf を使用してエンコードされます。 Protobuf は送信と受信が効率的ですが、バイナリ形式は人間が判読できません。 Protobuf では、 `*.proto`ファイルで指定されているメッセージのインターフェイスの説明を適切に逆シリアル化する必要があります。 ネットワーク上の Protobuf ペイロードを分析し、手動で要求を作成するために、追加のツールが必要です。

[サーバーリフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)や[grpc コマンドラインツール](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)などの機能は、バイナリ Protobuf メッセージを支援するために用意されています。 また、Protobuf メッセージは[JSON との間の変換を](https://developers.google.com/protocol-buffers/docs/proto3#json)サポートしています。 組み込みの JSON 変換は、デバッグ時に、Protobuf メッセージを人間が判読できる形式に変換するための効率的な方法を提供します。

## <a name="alternative-framework-scenarios"></a>その他のフレームワークのシナリオ

次のシナリオでは、gRPC よりも他のフレームワークをお勧めします。

* **ブラウザーでアクセス可能な api**&ndash; grpc はブラウザーで完全にはサポートされていません。 gRPC-Web はブラウザーサポートを提供できますが、制限があり、サーバープロキシも導入されています。
* **リアルタイム通信をブロードキャスト**する&ndash; grpc では、ストリーミングによるリアルタイム通信がサポートされていますが、登録済み接続へのメッセージのブロードキャストの概念は存在しません。 たとえば、チャットルーム内のすべてのクライアントに新しいチャットメッセージを送信するチャットルームの場合、新しいチャットメッセージをクライアントに個別にストリーミングするには、各 gRPC 呼び出しが必要です。 [SignalR](xref:signalr/introduction)は、このシナリオに便利なフレームワークです。 SignalR には、永続的な接続と、メッセージをブロードキャストするための組み込みサポートの概念があります。
* **プロセス間通信**&ndash;プロセスは、着信 grpc 呼び出しを受け入れるために、HTTP/2 サーバーをホストする必要があります。 Windows では、プロセス間通信[パイプ](/dotnet/standard/io/pipe-operations)は高速で軽量な通信方法です。

## <a name="additional-resources"></a>その他の技術情報

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
