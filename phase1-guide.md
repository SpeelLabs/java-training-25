# フェーズ1: Hello World サーブレット開発ガイド

## 目次
- [このフェーズの概要と目的](#このフェーズの概要と目的)
  - [このフェーズで達成すること](#このフェーズで達成すること)
  - [新しく学ぶ概念](#新しく学ぶ概念)
  - [作成するアプリケーションの構成](#作成するアプリケーションの構成)
- [1. プロジェクト構成と開発の準備](#1-プロジェクト構成と開発の準備)
  - [1.1 プロジェクト構造の作成](#11-プロジェクト構造の作成)
  - [1.2 pom.xmlファイルの作成](#12-pomxmlファイルの作成)
  - [1.3 Tomcatサーバーの設定](#13-tomcatサーバーの設定)
- [2. 最初のサーブレット作成](#2-最初のサーブレット作成)
  - [2.1 HelloServletクラスとは](#21-helloservletクラスとは)
  - [2.2 HelloServletクラスの実装](#22-helloservletクラスの実装)
  - [2.3 HTMLレスポンスの生成](#23-htmlレスポンスの生成)
  - [2.4 完全なHelloServletクラス](#24-完全なhelloservletクラス)
- [3. web.xmlの設定](#3-webxmlの設定)
  - [3.1 web.xmlの役割](#31-webxmlの役割)
  - [3.2 web.xmlの実装](#32-webxmlの実装)
- [4. サーブレットの動作確認](#4-サーブレットの動作確認)
  - [4.1 プロジェクトのビルド](#41-プロジェクトのビルド)
  - [4.2 Tomcatへのデプロイ](#42-tomcatへのデプロイ)
  - [4.3 アプリケーションへのアクセス](#43-アプリケーションへのアクセス)
- [5. クエリパラメータを受け取るサーブレット](#5-クエリパラメータを受け取るサーブレット)
  - [5.1 GreetingServletクラスとは](#51-greetingservletクラスとは)
  - [5.2 GreetingServletクラスの実装](#52-greetingservletクラスの実装)
  - [5.3 動作確認と検証](#53-動作確認と検証)
- [6. トラブルシューティング](#6-トラブルシューティング)
  - [6.1 404エラー（Not Found）](#61-404エラーnot-found)
  - [6.2 500エラー（Internal Server Error）](#62-500エラーinternal-server-error)
  - [6.3 コンパイルエラー](#63-コンパイルエラー)
  - [6.4 文字化けの問題](#64-文字化けの問題)
- [7. まとめと次のステップ](#7-まとめと次のステップ)
  - [7.1 フェーズ1のまとめ](#71-フェーズ1のまとめ)
  - [7.2 次のステップ](#72-次のステップ)

## このフェーズの概要と目的

フェーズ1では、Webアプリケーション開発の第一歩として、シンプルなサーブレットアプリケーションを開発します。サーブレットとは、Webサーバー上で動作するJavaプログラムで、HTTPリクエストを受け取り、レスポンスを返す仕組みです。初めてのサーブレットを作成することで、Webアプリケーションの基本的な動作原理を理解することが目標です。

### このフェーズで達成すること

1. 基本的なJava Webアプリケーション（WAR）プロジェクトの作成
2. シンプルなサーブレットの実装
3. 動的にHTMLを生成する方法の理解
4. URLパラメータを受け取って処理する方法の習得
5. Tomcatサーバーでのアプリケーション実行

### 新しく学ぶ概念

- **サーブレット**: Webリクエストを処理するJavaプログラム
- **HTTPリクエスト/レスポンス**: Webブラウザとサーバー間の通信方法
- **WAR（Web ARchive）**: Javaウェブアプリケーションの配布形式
- **デプロイ**: アプリケーションをサーバーに配置して実行可能にする作業
- **クエリパラメータ**: URLに含まれるデータ（?name=値 の形式）

### 作成するアプリケーションの構成

このフェーズでは次の2つのサーブレットを作成します：

1. **基本的な挨拶を表示するサーブレット（HelloServlet）**:
   - `/hello` というURLにアクセスすると「Hello, World!」というメッセージを表示
   - 静的なHTML内容を生成し、HTTPレスポンスとして返す

2. **名前をカスタマイズできる挨拶サーブレット（GreetingServlet）**:
   - `/greeting?name=田中` のようにURLパラメータを使ってアクセスすると「こんにちは、田中さん！」と表示
   - パラメータがない場合は「こんにちは、Guestさん！」というデフォルトメッセージを表示

---

## 1. プロジェクト構成と開発の準備

### 1.1 プロジェクト構造の作成

最初に、開発するアプリケーションのためのプロジェクト構造を作成します。VS Codeなどのエディタを使って、以下の構造になるようにフォルダとファイルを準備しましょう：

```
memo-app/
├── src/
│   └── main/
│       ├── java/              # Javaコードを格納するフォルダ
│       │   └── com/
│       │       └── example/
│       │           └── memo/
│       │               └── phase1/
│       └── webapp/           # Webリソースを格納するフォルダ
│           └── WEB-INF/
│               └── web.xml    # Webアプリの設定ファイル
├── pom.xml                    # Mavenプロジェクト設定ファイル
```

> 📝 **ヒント**: VS Codeの拡張機能「Maven for Java」や「Project Manager for Java」を使うと、このようなフォルダ構造を簡単に作成できます。これらの拡張機能をインストールして、新しいMavenプロジェクトを作成すると便利です。

### 1.2 pom.xmlファイルの作成

Maven（ビルドツール）の設定ファイルとして、プロジェクトのルートフォルダに `pom.xml` ファイルを作成します。このファイルには、プロジェクトの基本情報や依存するライブラリなどを記述します。段階的に必要な要素を見ていきましょう。

まず、XMLヘッダーとプロジェクトの基本情報を定義します：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>memo-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
</project>
```

> 📝 **ポイント**: `packaging`要素を`war`に設定することで、WARファイル（Webアプリケーションアーカイブ）形式でパッケージングすることを指定します。これはJavaのWebアプリケーションを配布する標準形式です。

次に、プロジェクトのプロパティを設定します。Javaのバージョンや文字コードなどを指定します：

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
</properties>
```

> 📝 **ヒント**: `maven.compiler.source`と`maven.compiler.target`は、コンパイルに使用するJavaのバージョンを指定します。JDK 21を使用していることを前提としていますが、使用するJDKのバージョンに合わせて変更できます。

続いて、プロジェクトの依存関係（使用するライブラリ）を指定します：

```xml
<dependencies>
    <!-- Jakarta Servlet API -->
    <dependency>
        <groupId>jakarta.servlet</groupId>
        <artifactId>jakarta.servlet-api</artifactId>
        <version>5.0.0</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

> 📝 **ポイント**: `scope`要素を`provided`に設定しているのは、この依存関係がコンパイル時には必要だが、実行時にはTomcatサーバーが提供するため含める必要がないことを示しています。これにより、最終的なWARファイルのサイズを小さくできます。

最後に、ビルド設定を指定します：

```xml
<build>
    <finalName>memo-app</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.3.2</version>
        </plugin>
    </plugins>
</build>
```

> 📝 **ヒント**: `finalName`要素は、生成されるWARファイルの名前を指定します。ここでは`memo-app.war`となります。これにより、URLが`http://localhost:8080/memo-app/...`という形式になります。

これらの要素をまとめた完全な`pom.xml`ファイルは以下の通りです：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>memo-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- Jakarta Servlet API -->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>5.0.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>memo-app</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.3.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

### 1.3 Tomcatサーバーの設定

サーブレットを実行するためには、Webアプリケーションサーバーが必要です。ここではApache Tomcatを使用します。VS Codeに「Community Server Connector」拡張機能をインストールし、Tomcatサーバーを設定します。

1. VS Codeの左側のメニューから拡張機能アイコンをクリック
2. 検索欄に「Community Server Connector」と入力し、インストール（すでにインストールされている場合はスキップ）
3. VS Code左側メニューのサーバーアイコンをクリック
4. 「+」ボタンをクリックしてサーバーを追加
5. 「Tomcat 10.x」を選択
6. Tomcatのインストールディレクトリを選択（通常は「C:\Program Files\Apache Software Foundation\Tomcat 10.0」など）

> ⚠️ **注意**: Tomcat 10はJakartaEE 9以降に対応しています。パッケージ名が`javax.servlet`から`jakarta.servlet`に変更されているため、コードでは`jakarta.servlet`を使用します。

---

## 2. 最初のサーブレット作成

### 2.1 HelloServletクラスとは

最初に作成する`HelloServlet`は、最もシンプルなサーブレットです。このサーブレットの役割は：

- `/hello`というURLパスに対応する
- ブラウザからのGETリクエストを処理する
- 「Hello, World!」というメッセージを含むHTMLページを返す

これによって、サーブレットの基本的な動作を理解することができます。

> 📝 **ポイント**: サーブレットとは、Webリクエストを処理しレスポンスを生成するJavaクラスです。従来のコンソールアプリケーションと異なり、ブラウザからのリクエストに応じて実行されるという点が特徴的です。

### 2.2 HelloServletクラスの実装

`HelloServlet`クラスには、データを保持するフィールド、リクエストを処理するためのメソッド、そして実際のHTMLを生成する処理を実装します。

まずは、基本となるサーブレットクラスの構造を作成します：

```java
package com.example.memo.phase1;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

// URLパターン「/hello」に対応するサーブレットクラスを定義
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    
    // GETリクエストを処理するメソッド
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // ここにHTMLレスポンスを生成するコードを追加
    }
}
```

> 📝 **ヒント**: `@WebServlet`アノテーションを使うと、URLパターンとサーブレットクラスの対応付けが簡単にできます。このアノテーションがなければ、web.xmlファイルに詳細な設定が必要になります。

作成したサーブレットクラスの重要な部分を詳しく説明します：

1. **@WebServlet アノテーション**:
   ```java
   @WebServlet("/hello")
   ```
   このアノテーションは、このサーブレットが `/hello` というURLパスに対応することを指定します。つまり、`http://localhost:8080/memo-app/hello` にアクセスすると、このサーブレットが実行されます。

2. **HttpServlet クラスの継承**:
   ```java
   public class HelloServlet extends HttpServlet {
   ```
   すべてのサーブレットは `HttpServlet` クラスを継承する必要があります。このクラスには、HTTP通信を処理するための基本的な機能が提供されています。

3. **doGet メソッドのオーバーライド**:
   ```java
   @Override
   protected void doGet(HttpServletRequest request, HttpServletResponse response) 
           throws ServletException, IOException {
   ```
   ブラウザからのGETリクエスト（通常のURLアクセス）を処理するためのメソッドです。`request` パラメータにはリクエスト情報、`response` パラメータにはレスポンス情報が含まれています。

> 📝 **ポイント**: サーブレットのライフサイクルは以下の通りです：
> 1. サーブレットクラスのロード（アプリケーション起動時）
> 2. サーブレットインスタンスの作成（最初のリクエスト時）
> 3. init()メソッドの呼び出し（初期化）
> 4. service()メソッドの呼び出し（各リクエスト時）- 内部でdoGetやdoPostなどを呼び出す
> 5. destroy()メソッドの呼び出し（終了時）
>
> 通常は`doGet`や`doPost`メソッドの実装だけを行えば、基本的なサーブレットは動作します。

### 2.3 HTMLレスポンスの生成

次に、レスポンスの設定とHTMLの生成処理を実装します：

```java
// レスポンスの内容タイプをHTMLに設定
response.setContentType("text/html;charset=UTF-8");

// レスポンス出力用のライターを取得
PrintWriter out = response.getWriter();
```

> 📝 **ポイント**: `setContentType`メソッドでは、ブラウザにどのようなデータが送信されるかを伝えています。ここでは「text/html」という形式で、文字コードは「UTF-8」であることを指定しています。これにより、ブラウザは受信したデータをHTMLとして解釈し、日本語も正しく表示できます。

最後に、実際のHTMLコンテンツを出力する処理を追加します：

```java
// HTMLを出力
out.println("<!DOCTYPE html>");
out.println("<html>");
out.println("<head>");
out.println("<title>Hello Servlet</title>");
out.println("</head>");
out.println("<body>");
out.println("<h1>Hello, World!</h1>");
out.println("<p>これは最初のサーブレットです。</p>");
out.println("</body>");
out.println("</html>");
```

> 📝 **ヒント**: `PrintWriter`の`println`メソッドを使うと、HTMLを1行ずつ出力できます。これは通常のコンソール出力（`System.out.println`）と似ていますが、出力先がブラウザに返されるHTTPレスポンスになっている点が異なります。

### 2.4 完全なHelloServletクラス

これらをまとめた完全な`HelloServlet`クラスを`src/main/java/com/example/memo/phase1/HelloServlet.java`に作成します：

```java
package com.example.memo.phase1;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

// URLパターン「/hello」に対応するサーブレットクラスを定義
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    
    // GETリクエストを処理するメソッド
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // レスポンスの内容タイプをHTMLに設定
        response.setContentType("text/html;charset=UTF-8");
        
        // レスポンス出力用のライターを取得
        PrintWriter out = response.getWriter();
        
        // HTMLを出力
        out.println("<!DOCTYPE html>");
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Hello Servlet</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h1>Hello, World!</h1>");
        out.println("<p>これは最初のサーブレットです。</p>");
        out.println("</body>");
        out.println("</html>");
    }
}
```

---

## 3. web.xmlの設定

### 3.1 web.xmlの役割

`web.xml` は、Webアプリケーションの設定ファイルです。このファイルに、サーブレットやフィルター、セッション管理などの設定を記述します。最近のJakarta EEでは、アノテーションで多くの設定ができるようになり、`web.xml` は簡素化されています。

ただし、基本的な設定は記述しておくと良いでしょう。`web.xml` の主な役割：

- Webアプリケーションの基本情報の定義
- デフォルトページ（ウェルカムファイル）の指定
- エラーページの設定
- セッションのタイムアウト時間の設定
- その他、アプリケーション全体に関わる設定

### 3.2 web.xmlの実装

`web.xml`ファイルは、Webアプリケーションの設定を定義するXMLファイルです。段階的に構成要素を見ていきましょう。

まず、XMLヘッダーと基本的な設定を記述します：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">
</web-app>
```

次に、アプリケーションの表示名を設定します：

```xml
<display-name>Memo Application</display-name>
```

最後に、ウェルカムファイル（デフォルトで表示されるファイル）を指定します：

```xml
<welcome-file-list>
    <welcome-file>index.html</welcome-file>
</welcome-file-list>
```

これらをまとめた完全な`web.xml`ファイルを`src/main/webapp/WEB-INF/web.xml`に作成します：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <display-name>Memo Application</display-name>
    
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
    </welcome-file-list>

</web-app>
```

> 📝 **ヒント**: `@WebServlet` アノテーションを使用している場合、サーブレットのマッピングを `web.xml` に記述する必要はありません。コードとURLのマッピングが一目で分かるため、アノテーション方式の方が分かりやすいでしょう。

---

## 4. サーブレットの動作確認

### 4.1 プロジェクトのビルド

作成したコードをコンパイルして、Webアプリケーションアーカイブ（WAR）を生成します。VS Codeでターミナルを開き（`Ctrl+`` または上部メニューの「ターミナル」→「新しいターミナル」）、以下のコマンドを実行します：

```bash
mvn clean package
```

> 📝 **ヒント**: このコマンドは2つの部分から成り立っています。`clean`は以前のビルド結果を削除し、`package`は新しくビルドしてWARファイルを作成します。ビルドが成功すると、`target/memo-app.war`ファイルが生成されます。

### 4.2 Tomcatへのデプロイ

生成したWARファイルをTomcatサーバーにデプロイ（配置）します：

1. VS Code左側のサーバーアイコンをクリック
2. Tomcatサーバーを右クリックし、「Add Deployment」を選択
3. ビルドで生成された `target/memo-app.war` ファイルを選択
4. コンテキストパスを `/memo-app` に設定（これはWebアプリのURLパスとなります）

### 4.3 アプリケーションへのアクセス

Tomcatサーバーを起動し、アプリケーションにアクセスします：

1. Tomcatサーバーを右クリックし、「Start」をクリック（すでに起動している場合は「Restart」）
2. サーバーが起動したら、ブラウザで以下のURLにアクセス:
   - `http://localhost:8080/memo-app/hello`

「Hello, World!」と表示されたページが表示されれば成功です！これで最初のサーブレットアプリケーションが正常に動作していることを確認できました。

> 📝 **ヒント**: サーバーの起動状態や、起動中のログを確認するには、VS Codeの出力タブ（「表示」→「出力」）で「Tomcat」や「Community Server Connector」を選択します。

---

## 5. クエリパラメータを受け取るサーブレット

### 5.1 GreetingServletクラスとは

より動的なWebアプリケーションを作成するために、URLパラメータを受け取るサーブレットを作成します。`GreetingServlet` は次の機能を持ちます：

- `/greeting` というURLパスに対応
- URLパラメータ `name` の値を取得（例：`/greeting?name=田中`）
- パラメータの値を使って、個別の挨拶メッセージを表示
- パラメータがない場合はデフォルト値「Guest」を使用

これにより、ユーザーごとにカスタマイズされたメッセージを表示することができます。

### 5.2 GreetingServletクラスの実装

`GreetingServlet`クラスは、URLパラメータから名前を取得し、その名前を使って挨拶メッセージを表示します。段階的に実装していきましょう。

まず、基本となるサーブレットクラスの構造を作成します：

```java
package com.example.memo.phase1;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/greeting")
public class GreetingServlet extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        // ここにリクエスト処理のコードを追加
    }
}
```

次に、URLパラメータの取得と処理を実装します：

```java
// URLパラメータ「name」の値を取得
String name = request.getParameter("name");

// nameパラメータが指定されていない場合のデフォルト値
if (name == null || name.isEmpty()) {
    name = "Guest";
}

// レスポンスの設定
response.setContentType("text/html;charset=UTF-8");
PrintWriter out = response.getWriter();
```

> 📝 **ポイント**: `request.getParameter()`メソッドを使うと、URLパラメータの値を取得できます。このメソッドは指定したパラメータが存在しない場合は`null`を返すため、必ず存在チェックを行うことが重要です。例えば`http://localhost:8080/memo-app/greeting?name=田中`というURLの場合、`name`の値は「田中」になります。

最後に、取得した名前を使用してHTMLコンテンツを生成します：

```java
// HTMLを出力
out.println("<!DOCTYPE html>");
out.println("<html>");
out.println("<head>");
out.println("<title>Greeting Servlet</title>");
out.println("</head>");
out.println("<body>");
out.println("<h1>こんにちは、" + name + "さん！</h1>");
out.println("<p>Servletからの挨拶です。</p>");
out.println("<p><a href=\"hello\">Hello Servletへ戻る</a></p>");
out.println("</body>");
out.println("</html>");
```

これらをまとめた完全な`GreetingServlet`クラスを`src/main/java/com/example/memo/phase1/GreetingServlet.java`に作成します：

### 5.3 動作確認と検証

ブラウザで以下のようなURLにアクセスして、動作を確認します：

1. パラメータありの場合:
   - `http://localhost:8080/memo-app/greeting?name=田中`
   - 表示: 「こんにちは、田中さん！」

2. パラメータなしの場合:
   - `http://localhost:8080/memo-app/greeting`
   - 表示: 「こんにちは、Guestさん！」

3. 別の名前を試す:
   - `http://localhost:8080/memo-app/greeting?name=鈴木`
   - 表示: 「こんにちは、鈴木さん！」

このように、URLパラメータによって動的にコンテンツを変更することができました。これがWebアプリケーションの基本的な動作の一つです。

> 📝 **ポイント**: URLパラメータは、`?パラメータ名=値` の形式で指定します。複数のパラメータは `&` で区切ります（例：`?name=山田&age=30`）。

---

## 6. トラブルシューティング

開発中によく遭遇する問題と、その解決方法を紹介します。

### 6.1 404エラー（Not Found）

ブラウザで「404 Not Found」と表示される場合：

**原因**: 
- URLが間違っている
- サーブレットが正しく設定されていない
- アプリケーションが正しくデプロイされていない

**解決策**:
- URLが正しいか確認（大文字小文字も含めて）
- `/memo-app/hello` の形式になっているか確認
- `@WebServlet` アノテーションのパスが正しいか確認
- Tomcatのログで詳細なエラーを確認

> 📝 **ヒント**: 404エラーは「リソースが見つからない」ことを示します。URLのスペルミスや大文字小文字の違いも原因になることがあります。また、`web.xml`の設定やアノテーションの記述も確認しましょう。

### 6.2 500エラー（Internal Server Error）

ブラウザで「500 Internal Server Error」と表示される場合：

**原因**:
- サーブレット内で例外が発生している
- `web.xml` の設定が不正

**解決策**:
- Tomcatのログを確認して具体的なエラーメッセージを特定
- コード内の例外処理を見直す
- `web.xml` の構文を確認

> 📝 **ポイント**: 500エラーは「サーバー側でエラーが発生した」ことを示します。詳細なエラー原因はサーバーのログファイルを確認することで特定できます。典型的な原因としては、NullPointerExceptionやClassCastExceptionなどの実行時例外があります。

### 6.3 コンパイルエラー

ビルド時に「コンパイルエラー」が発生する場合：

**原因**:
- Javaの構文ミス
- 必要なライブラリが不足している
- パッケージ構造やインポートが間違っている

**解決策**:
- エラーメッセージを確認して該当する箇所を修正
- `pom.xml` に必要な依存関係が定義されているか確認
- パッケージ宣言とフォルダ構造が一致しているか確認

> 📝 **ヒント**: コンパイルエラーはビルド時に発生するエラーで、コードが実行される前に検出されます。エラーメッセージには通常、問題のあるファイル名と行番号が含まれているので、そこを確認しましょう。セミコロンの欠落や括弧の対応ミスなどが一般的な原因です。

### 6.4 文字化けの問題

日本語が文字化けする場合：

**原因**:
- 文字コードの設定不足
- レスポンスのエンコーディング設定がない

**解決策**:
- `response.setContentType("text/html;charset=UTF-8");` が設定されているか確認
- ファイルがUTF-8で保存されているか確認
- HTMLの`<meta charset="UTF-8">` タグが正しく設定されているか確認

> 📝 **ポイント**: 文字化けを防ぐには、「ファイルの保存時の文字コード」と「レスポンスで指定する文字コード」の両方を同じ（UTF-8を推奨）に揃えることが重要です。VS Codeの右下に表示される文字コード設定も確認しましょう。

---

## 7. まとめと次のステップ

### 7.1 フェーズ1のまとめ

このフェーズでは、基本的なWebアプリケーション開発の第一歩として、以下のことを学びました：

1. **サーブレットの基本構造と動作原理**:
   - `HttpServlet` を継承したクラスの作成
   - `doGet` メソッドでのHTTPリクエスト処理
   - `WebServlet` アノテーションによるURL設定

2. **HTMLレスポンスの生成方法**:
   - `PrintWriter` を使ったHTML出力
   - レスポンスタイプと文字コードの設定

3. **URLパラメータの処理**:
   - `request.getParameter()` を使ったパラメータ取得
   - 動的なコンテンツ生成

4. **Mavenプロジェクトの構築と管理**:
   - `pom.xml` の設定
   - プロジェクトのビルドコマンド

5. **Tomcatへのデプロイと実行**:
   - WARファイルの生成と配置
   - アプリケーションへのアクセス確認

これらの基本的な知識と技術は、これからのWebアプリケーション開発の土台となります。

### 7.2 次のステップ

基本的なサーブレットの開発ができたら、以下のような発展的な内容に取り組んでみましょう：

1. **サーブレットの機能拡張**:
   - 複数のURLパラメータを処理する
   - 数値計算などの処理を追加する
   - リダイレクトやフォワードの実装

2. **HTMLの改善**:
   - CSSを使ったデザインの改善
   - フォームを含むページの作成
   - JavaScriptを使った動的な要素の追加

3. **次のフェーズへの準備**:
   - JSP（JavaServer Pages）の基本を調べる
   - MVCモデルについて学ぶ
   - データベース連携の基礎知識
