# llm-presentation-direct-output

## 1\. 目的

このドキュメントと付随する `odp-spec.yaml` ファイルは、大規模言語モデル（LLM）が **OpenDocument Presentation (.odp) を構成するファイル群を直接生成**できるようにするためのナレッジベースです。

最終的な `.odp` ファイルは、ここで生成されたファイル群をZIP圧縮したものです。このナレッジは、圧縮前の各構成ファイル（`content.xml`, `styles.xml` など）の構造と内容を定義します。

## 2\. ODPの基本概念

ODPファイルは、単一のファイルではなく、特定のディレクトリ構造を持つファイルとディレクトリの集合体をZIPで圧縮したパッケージです。

中核的な設計思想は「関心の分離」であり、主要な情報は以下のXMLファイルに分割されています。

  - **`content.xml`**: スライド、テキスト、図形などのコンテンツそのもの。
  - **`styles.xml`**: 見た目を定義するスタイル（レイアウト、フォント、色など）。
  - **`meta.xml`**: 作成者や作成日時などのメタデータ。
  - **`settings.xml`**: アプリケーション固有の設定（ズームレベルなど）。

## 3\. LLM向け利用ガイド

ユーザーからの要求に応じてODPのファイル群を生成する際は、以下の手順に従ってください。

1.  **`odp-spec.yaml` を参照**: このナレッジベースの核心は `odp-spec.yaml` です。ファイル構造、XML要素、必須属性、親子関係など、生成に必要なすべての技術仕様が構造化されています。常にこのYAMLファイルを**唯一の信頼できる情報源**としてください。

2.  **ディレクトリ構造の作成**: まず、基本的なファイルとディレクトリの構造を構築します。最小構成は以下の通りです。

    ```
    /
    ├── mimetype
    ├── META-INF/
    │   └── manifest.xml
    ├── content.xml
    ├── styles.xml
    ├── meta.xml
    └── settings.xml
    ```

    画像などを含む場合は `Pictures/` ディレクトリも作成します。

3.  **各ファイルの生成**: `odp-spec.yaml` の定義に基づき、各ファイルを生成します。

      - **`mimetype`**: `package_structure.mimetype.content` に定義された文字列をそのまま記述します。
      - **`META-INF/manifest.xml`**: パッケージに含めるすべてのファイル（`mimetype`と`manifest.xml`自身を除く）のエントリを `<manifest:file-entry>` として記述します。
      - **XMLファイル (`content.xml`, `styles.xml`, etc.)**:
          - YAMLに定義されたルート要素から始めます（例: `content_xml.root_element`）。
          - YAMLの階層構造に従い、必要な子要素と属性を追加します。
          - `namespaces`セクションで定義されているXML名前空間プレフィックスを正しく使用してください。

## 4\. 生成リクエストの実行例

以下に、ユーザーからの典型的なリクエストと、それに対するLLMの理想的な出力形式を示します。

-----

**ユーザーリクエスト:**

> 「タイトルが『プレゼンテーションのテスト』、本文が『これは最初のスライドです。』という内容の、シンプルなスライドを1枚持つODPファイル群を生成してください。」

-----

**LLMの出力:**

以下に、要求されたODPプレゼンテーションを構成するファイル群を生成します。

**ディレクトリ構造:**

```
/
├── mimetype
├── META-INF/
│   └── manifest.xml
├── content.xml
├── styles.xml
├── meta.xml
└── settings.xml
```

-----

**ファイル名: `mimetype`**

```
application/vnd.oasis.opendocument.presentation
```

-----

**ファイル名: `META-INF/manifest.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest:manifest xmlns:manifest="urn:oasis:names:tc:opendocument:xmlns:manifest:1.0">
  <manifest:file-entry manifest:full-path="/" manifest:media-type="application/vnd.oasis.opendocument.presentation"/>
  <manifest:file-entry manifest:full-path="content.xml" manifest:media-type="text/xml"/>
  <manifest:file-entry manifest:full-path="styles.xml" manifest:media-type="text/xml"/>
  <manifest:file-entry manifest:full-path="meta.xml" manifest:media-type="text/xml"/>
  <manifest:file-entry manifest:full-path="settings.xml" manifest:media-type="text/xml"/>
</manifest:manifest>
```

-----

**ファイル名: `content.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<office:document-content xmlns:office="urn:oasis:names:tc:opendocument:xmlns:office:1.0" xmlns:draw="urn:oasis:names:tc:opendocument:xmlns:drawing:1.0" xmlns:presentation="urn:oasis:names:tc:opendocument:xmlns:presentation:1.0" xmlns:text="urn:oasis:names:tc:opendocument:xmlns:text:1.0" xmlns:svg="urn:oasis:names:tc:opendocument:xmlns:svg-compatible:1.0">
  <office:automatic-styles/>
  <office:body>
    <office:presentation>
      <draw:page draw:master-page-name="mp1">
        <draw:text-box presentation:class="title" svg:x="2cm" svg:y="2cm" svg:width="24cm" svg:height="4cm">
          <text:p>プレゼンテーションのテスト</text:p>
        </draw:text-box>
        <draw:text-box presentation:class="outline" svg:x="2cm" svg:y="7cm" svg:width="24cm" svg:height="12cm">
          <text:p>これは最初のスライドです。</text:p>
        </draw:text-box>
      </draw:page>
    </office:presentation>
  </office:body>
</office:document-content>
```

-----

**ファイル名: `styles.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<office:document-styles xmlns:office="urn:oasis:names:tc:opendocument:xmlns:office:1.0" xmlns:style="urn:oasis:names:tc:opendocument:xmlns:style:1.0" xmlns:fo="urn:oasis:names:tc:opendocument:xmlns:xsl-fo-compatible:1.0">
  <office:automatic-styles>
    <style:page-layout style:name="pm1">
      <style:page-layout-properties fo:page-width="28cm" fo:page-height="21cm" style:print-orientation="landscape"/>
    </style:page-layout>
  </office:automatic-styles>
  <office:master-styles>
    <style:master-page style:name="mp1" style:page-layout-name="pm1"/>
  </office:master-styles>
</office:document-styles>
```

-----

**ファイル名: `meta.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<office:document-meta xmlns:office="urn:oasis:names:tc:opendocument:xmlns:office:1.0" xmlns:meta="urn:oasis:names:tc:opendocument:xmlns:meta:1.0" xmlns:dc="http://purl.org/dc/elements/1.1/">
  <office:meta>
    <meta:generator>LLM-ODP-Generator/1.0</meta:generator>
    <dc:title>プレゼンテーションのテスト</dc:title>
  </office:meta>
</office:document-meta>
```

-----

**ファイル名: `settings.xml`**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<office:document-settings xmlns:office="urn:oasis:names:tc:opendocument:xmlns:office:1.0" xmlns:config="urn:oasis:names:tc:opendocument:xmlns:config:1.0">
  <office:settings/>
</office:document-settings>
```

-----

## 5\. ライセンスと帰属

このナレッジベースは、OASIS (Organization for the Advancement of Structured Information Standards) によって策定された **Open Document Format for Office Applications (OpenDocument) v1.3** 仕様に基づいています。

元の仕様書に関する著作権は OASIS Open 2021 に帰属します。OASISの利用許諾に基づき、仕様書を解説または実装を支援する目的で派生物を作成し、配布することが許可されています。

このドキュメントは、ODF仕様の要約であり、完全性や正確性を保証するものではありません。正確な情報については、必ず公式のOASIS標準ドキュメントを参照してください。
