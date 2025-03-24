# フェーズ1: Hello World サーブレット開発ガイド

## 学習の概要

### 目標
このフェーズでは、Webアプリケーション開発の第一歩として、シンプルなサーブレットアプリケーションを開発します。初めてのサーブレットを作成することで、Webアプリケーションの基本的な動作原理を理解することが目標です。

### 作るもの
1. **基本的な挨拶を表示するサーブレット**
   - ブラウザから「/hello」というURLにアクセスすると「Hello, World!」というメッセージを表示する画面が表示されます
   - 静的なHTML内容を生成し、HTTPレスポンスとして返します
   - Webアプリケーションの最も基本的な機能を体験できます

2. **名前をカスタマイズできる挨拶サーブレット**
   - 「/greeting?name=山田」のように、URLにパラメータを含めてアクセスすると「Hello, 山田!」と表示されます
   - URLからパラメータを受け取り、その値に応じて動的にコンテンツを生成します
   - パラメータがない場合は「Hello, Guest!」というデフォルトメッセージを表示します

### 学ぶこと
- Javaサーブレットの基本構造と役割
- HTTPリクエストとレスポンスの仕組み
- Webサーバー（Tomcat）の基本的な使い方
- URLパラメータの取得と処理方法
- HTML形式のレスポンス生成方法

### 前提知識
- Javaの基本文法（変数、クラス、メソッドなど）
- HTMLの基本的な構造

### 開発環境
このガイドでは以下の環境を使用します：
- JDK 21
- Apache Tomcat 10
- Maven 3.9.9
- VS Code + Community Server Connector

このガイドでは、Javaサーブレットの基本を学び、最初のWebアプリケーションを作成します。サーブレットとは、Webサーバー上で動作するJavaプログラムで、HTTPリクエストを受け取り、レスポンスを返す仕組みです。

---

## 目次
1. [プロジェクトの作成](#1-プロジェクトの作成)
2. [Tomcatサーバーの設定](#2-tomcatサーバーの設定)
3. [最初のサーブレット作成](#3-最初のサーブレット作成)
4. [web.xmlの設定](#4-webxmlの設定)
5. [サーブレットの動作確認](#5-サーブレットの動作確認)
6. [クエリパラメータを受け取るサーブレット](#6-クエリパラメータを受け取るサーブレット)
7. [よくあるエラーと解決方法](#7-よくあるエラーと解決方法)

---

## 1. プロジェクトの作成

### 1.1 プロジェクト構造を作成する

VS Codeで新しいフォルダを作成し、以下の構造になるようにフォルダとファイルを準備します：

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
├── pom.xml                    # Mavenプロジェクト設定ファイル（ライブラリの管理）
```

### 1.2 `pom.xml` ファイルを作成する

プロジェクトのルートフォルダに `pom.xml` ファイルを作成し、以下の内容をコピーします：

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

このpom.xmlファイルは、プロジェクトの依存関係（使用するライブラリ）とビルド設定を定義します。

## 2. Tomcatサーバーの設定

### 2.1 VS Codeへの「Community Server Connector」拡張機能の追加

既に「Community Server Connector」拡張機能がインストールされている前提ですが、確認のため手順を示します：

1. VS Codeの左側のメニューから拡張機能アイコンをクリック
2. 検索欄に「Community Server Connector」と入力
3. インストールされていることを確認

### 2.2 Tomcatサーバーの設定

1. VS Code左側メニューのサーバーアイコンをクリック
2. 「+」ボタンをクリックしてサーバーを追加
3. 「Tomcat 10.x」を選択
4. Tomcatのインストールディレクトリを選択
   （通常は「C:\Program Files\Apache Software Foundation\Tomcat 10.0」など）

## 3. 最初のサーブレット作成

### 3.1 HelloServletクラスの作成

`src/main/java/com/example/memo/phase1/HelloServlet.java` ファイルを作成し、以下のコードを追加します：

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

このサーブレットは、ブラウザからアクセスされると「Hello, World!」というメッセージを含むHTML画面を表示します。

### コードの説明：

- `@WebServlet("/hello")`: このアノテーションは、このサーブレットが `/hello` というURLパスに対応することを指定します。
- `extends HttpServlet`: HttpServletクラスを継承することで、このクラスはサーブレットとして機能します。
- `doGet` メソッド: GETリクエストを処理するためのメソッドで、ブラウザからのリクエストに対してレスポンスを返します。
- `response.setContentType`: レスポンスのコンテンツタイプを「text/html」に設定します。
- `PrintWriter out = response.getWriter()`: レスポンスにテキストを書き込むためのオブジェクトを取得します。
- `out.println`: HTMLコードをレスポンスに書き込みます。

## 4. web.xmlの設定

`src/main/webapp/WEB-INF/web.xml` ファイルを作成し、以下の内容を追加します：

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

`web.xml` は、Webアプリケーションの設定ファイルです。ただし、`@WebServlet` アノテーションを使用しているため、サーブレットの設定はこのファイルに書く必要はありません。

---

## 5. サーブレットの動作確認

### 5.1 プロジェクトのビルド

VS Codeでターミナルを開き（Ctrl+`）、以下のコマンドを実行してプロジェクトをビルドします：

```bash
# プロジェクトのルートフォルダに移動していることを確認して
mvn clean package
```

正常にビルドが完了すると、`target` フォルダに `memo-app.war` ファイルが生成されます。

### 5.2 Tomcatへのデプロイ

1. VS Code左側のサーバーアイコンをクリック
2. Tomcatサーバーを右クリックし、「Add Deployment」を選択
3. ビルドで生成された `target/memo-app.war` ファイルを選択
4. コンテキストパスを `/memo-app` に設定（これはWebアプリのURLパスとなります）

### 5.3 サーバーの起動とアクセス

1. Tomcatサーバーを右クリックし、「Start」をクリック
2. サーバーが起動したら、ブラウザで以下のURLにアクセス:
   - `http://localhost:8080/memo-app/hello`

「Hello, World!」と表示されたページが表示されれば成功です！

---

## 6. クエリパラメータを受け取るサーブレット

### 6.1 GreetingServletクラスの作成

`src/main/java/com/example/memo/phase1/GreetingServlet.java` ファイルを作成し、以下のコードを追加します：

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
        
        // URLパラメータ「name」の値を取得
        String name = request.getParameter("name");
        
        // nameパラメータが指定されていない場合のデフォルト値
        if (name == null || name.isEmpty()) {
            name = "Guest";
        }
        
        // レスポンスの設定
        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();
        
        // HTMLを出力
        out.println("<!DOCTYPE html>");
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Greeting Servlet</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h1>Hello, " + name + "!</h1>");
        out.println("<p>Servletからの挨拶です。</p>");
        out.println("<p><a href=\"hello\">Hello Servletへ戻る</a></p>");
        out.println("</body>");
        out.println("</html>");
    }
}
```

このサーブレットは、`name`というクエリパラメータを受け取り、その値を使用して「Hello, [name]!」というメッセージを表示します。

### コードの説明：

- `request.getParameter("name")`: URLパラメータ「name」の値を取得します。
- `if (name == null || name.isEmpty())`: パラメータが指定されていない場合の処理です。
- `out.println("<h1>Hello, " + name + "!</h1>")`: パラメータの値を使ってHTMLを生成します。

### 6.2 プロジェクトの再ビルドとデプロイ

1. ターミナルで以下のコマンドを実行してプロジェクトを再ビルドします：
   ```bash
   mvn clean package
   ```

2. Tomcatサーバーを再起動（停止後に起動）します。

### 6.3 動作確認

ブラウザで以下のURLにアクセスします：
- `http://localhost:8080/memo-app/greeting?name=John`

「Hello, John!」というメッセージが表示されればOKです。

パラメータを変更して試してみましょう：
- `http://localhost:8080/memo-app/greeting?name=田中`

また、パラメータなしでアクセスすると、デフォルト値「Guest」が使用されます：
- `http://localhost:8080/memo-app/greeting`

---

## 7. よくあるエラーと解決方法

### 7.1 404エラー（Not Found）

**原因**: 
- URLが間違っている
- サーブレットのマッピングが正しく設定されていない
- アプリケーションが正しくデプロイされていない

**解決策**:
- URLが正しいか確認（大文字小文字も含めて）
- `@WebServlet` アノテーションのパスが正しいか確認
- Tomcatのログで詳細なエラーを確認

### 7.2 500エラー（Internal Server Error）

**原因**:
- サーブレット内で例外が発生している
- `web.xml` の設定が不正

**解決策**:
- Tomcatのログを確認して具体的なエラーメッセージを特定
- コード内の例外処理を見直す
- `web.xml` の構文を確認

### 7.3 コンパイルエラー

**原因**:
- Javaの構文ミス
- 必要なライブラリが不足している
- パッケージ構造やインポートが間違っている

**解決策**:
- エラーメッセージを確認して該当する箇所を修正
- `pom.xml` に必要な依存関係が定義されているか確認
- パッケージ宣言とフォルダ構造が一致しているか確認

---

## まとめ

おめでとうございます！これで最初のサーブレットアプリケーションが完成しました。このフェーズでは以下のことを学びました：

1. Javaサーブレットの基本構造と動作原理
2. HTTPリクエストの処理方法
3. HTMLレスポンスの生成方法
4. URLパラメータの取得と利用方法

次のフェーズでは、JSPを使ったビューの実装と、HTMLフォームからデータを送信する方法を学びます。

## 次のステップ

- サーブレットに新しい機能を追加してみましょう
- 複数のパラメータを受け取る処理を試してみましょう
- HTMLの内容を工夫して、より見栄えの良いページを作ってみましょう
