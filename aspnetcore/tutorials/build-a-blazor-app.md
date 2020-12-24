---
title: Blazor Todo リスト アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870439"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Blazor Todo リスト アプリを構築する

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。 以下の方法について説明します。

> [!div class="checklist"]
> * Todo リストの Blazor アプリ プロジェクトを作成する
> * Razor コンポーネントを変更する
> * コンポーネントでイベント処理とデータ バインディングを使用する
> * Blazor アプリでルーティングを使用する

このチュートリアルの最後には、動作する ToDo リスト アプリが完成します。

## <a name="prerequisites"></a>前提条件

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>ToDo リスト Blazor アプリを作成する

1. コマンド シェルで `TodoList` という名前の新しい Blazor アプリを作成します。

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   上記のコマンドでは、アプリを保持するための `TodoList` という名前のフォルダーが作成されます。 `TodoList` フォルダーは、プロジェクトの *ルート フォルダー* です。 次のコマンドを使用して、ディレクトリを `TodoList` フォルダーに変更します。

   ```dotnetcli
   cd TodoList
   ```

1. 次のコマンドを使用して、新しい `Todo` Razor コンポーネントを、`Pages` フォルダー内のアプリに追加します。

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor コンポーネント ファイル名の先頭文字は、大文字である必要があります。 `Pages` フォルダーを開き、`Todo` コンポーネントのファイル名の先頭が大文字 `T` であることを確認します。 ファイル名は `Todo.razor` のはずです。

1. `Pages/Todo.razor` で、コンポーネントの最初のマークアップを指定します。

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   `Pages/Todo.razor` ファイルを保存します。

1. ナビゲーション バーに `Todo` コンポーネントを追加します。

   `NavMenu` コンポーネント (`Shared/NavMenu.razor`) はアプリのレイアウトで使用されます。 レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。

   `Shared/NavMenu.razor` ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加し、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   `Shared/NavMenu.razor` ファイルを保存します。

1. `TodoList` フォルダーからコマンド シェルで [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) コマンドを実行して、アプリをビルドして実行します。 `https://localhost:5001/todo` の新しい Todo ページに移動して、サイドバーにある `Todo` コンポーネントへのナビゲーション リンクが機能することを確認します。

1. Todo アイテムを表すクラスを保持するために、プロジェクト (`TodoList` フォルダー) のルートに `TodoItem.cs` ファイルを追加します。 `TodoItem` クラス用に次の C# コードを使います。

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. `Todo` コンポーネント (`Pages/Todo.razor`) に戻ります。

   * Todo 項目用のフィールドを `@code` ブロックに追加します。 `Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。
   * 各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. アプリには、リストに Todo 項目を追加するための UI 要素が必要です。 順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. `TodoItem.cs` ファイルと更新された `Pages/Todo.razor` ファイルを保存します。 コマンド シェルでは、ファイルが保存されるとアプリが自動的に再構築されます。 ブラウザーからアプリへの接続は一時的に失われ、接続が再確立されるとページが再度読み込まれます。

1. **`Add todo`** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーがアタッチされていないためです。

1. `Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。 ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. 新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=3)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. 指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。 `newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. `Pages/ToDo.razor` ファイルを保存します。 このアプリは、コマンド シェルで自動的にリビルドされます。 ブラウザーからアプリに再接続された後、ページがブラウザーに再度読み込まれます。

1. 各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。 各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。 `@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. それらの値がバインドされていることを確認するために、`<h3>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. 完成した `Todo` コンポーネント (`Pages/Todo.razor`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. `Pages/ToDo.razor` ファイルを保存します。 このアプリは、コマンド シェルで自動的にリビルドされます。 ブラウザーからアプリに再接続された後、ページがブラウザーに再度読み込まれます。

1. Todo アイテムを追加して新しいコードをテストします。

1. 完了したら、コマンド シェルでアプリをシャットダウンします。 多くのコマンド シェルでは、<kbd>Ctrl</kbd>+<kbd>c</kbd> キーボード コマンドを使用してアプリを停止できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Todo リストの Blazor アプリ プロジェクトを作成する
> * Razor コンポーネントを変更する
> * コンポーネントでイベント処理とデータ バインディングを使用する
> * Blazor アプリでルーティングを使用する

ASP.NET Core Blazor 用のツールについて学習します。

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
