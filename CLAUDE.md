# 罫線帳 (keisencho.com) — 運用メモ

日本株をテーマにした個人ブログ。ユーザー(433numa / numachi-works)の副業サイト。
静的HTML、ビルドツールなし。GitHub: numachi-works/keisen-cho。Netlify配信。

## 全権委任の合意(重要)

ユーザーから技術メンテナンス・記事の執筆/公開を含め**完全にお任せ**されている。
- 事前に許可を取らずに判断・実行してよい(記事のテーマ選定、執筆、公開含む)
- 何かやったら**事後報告**する(やったこと/タイトル/要約)
- ただし以下は私にはできないので、必要なときはユーザーに具体的に伝える:
  - Netlifyダッシュボードでの操作
  - Google Search Console UIでの操作(プロパティ追加、確認コード貼り付けなど)
  - AdSenseアカウントでの操作
- WebFetchのallowlist制限で本番サイト(keisencho.com)を直接閲覧できないことが多い。
  確認が必要な場合はユーザーにスクリーンショットをお願いする。

## インフラ

- Netlifyプロジェクト名: preeminent-pegasus-90885a (project id: 342f0f2e-78ff-40ab-b64a-c3710054504b)
- カスタムドメイン **keisencho.com が正式な主ドメイン**(primarySiteUrl)。
  netlify.app サブドメインへのアクセスはkeisencho.comへ自動リダイレクトされる。
  → サイト内の全リンク・OGP・sitemap・JSON-LDは必ずkeisencho.comを使うこと。
- GitHub Search Console: keisencho.com のURLプレフィックスプロパティを2026/10/22に作成・確認済み。
  sitemap (https://keisencho.com/sitemap.xml) 送信済み。トップページはインデックス登録済み確認済み。
  新規プロパティなので「ページ」レポートが「未処理」多数でもしばらくは正常。
- AdSense: publisher ID `pub-8069668191912447`。keisencho.comをサイトとして登録し審査リクエスト済み
  (2026/10頃、結果待ち)。ads.txt設置済み: `google.com, pub-8069668191912447, DIRECT, f08c47fec0942fa0`

## Git コミット規約

必ずこの形式でコミット・push(著者情報はユーザー、Co-Authored-Byは固定):

```
git -c user.email="333numa@gmail.com" -c user.name="numachi-works" commit -m "$(cat <<'EOF'
<コミットメッセージ>

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01Lm5sjbP37QaFGDhR6XZ5Ev
EOF
)"
git push
```

(Claude-Session のIDはセッションごとに変わるので、その時点のセッションIDを使う)

## 新規記事を作るときのチェックリスト

1. 既存記事(例: toyota-7203.html, glossary-choki-kinri.html)を1つ読んでテンプレート構造を踏襲する
2. `<head>`: gtag.js、favicon link(下記)、viewport、title、meta description、OGP/Twitter Card
   (og:url等は必ずkeisencho.com)、adsbygoogle script、inline `<style>`、JSON-LD(下記)
3. favicon: `<link rel="icon" type="image/png" href="/favicon.png">` と
   `<link rel="apple-touch-icon" href="/apple-touch-icon.png">` をcharsetメタの直後に挿入
4. JSON-LD (Article型、schema.org)を`</head>`直前に挿入。author/publisherは
   Organization「罫線帳」、logo は https://keisencho.com/favicon.png。
   datePublished/dateModifiedは新規なら同じ日時、更新なら `git log --follow --format=%aI` で確認
5. body構造: `.wrap` > header/nav/article/footer。article内は `.lead` 導入文 →
   複数の `<h2 class="sec">` セクション(`.figures` で数字を箱表示することも) →
   `関連記事` ボックス(背景色 #DED4B8、既存記事への内部リンク2〜3件、相互リンクも追加すること) →
   シェアボタン行(X/LINE/はてな、urllib.parse.quoteで正しくURLエンコード) →
   「証券口座をお持ちでない方へ」PR枠(下記) → `.disclaimer`
6. PR枠のアフィリエイトリンク: TOSSYは固定リンク。DMM(`a8mat=4BEA4S+FDP5DE+1WP2+<code>`)と
   松井証券(`a8mat=4BEA4S+EY7VN6+3XCC+<code>`)はコードをローテーションする。
   **使う前に必ず** `grep -oh` で全HTML内の出現回数を数え、最も使用回数が少ないコードを選ぶこと。
   各コードには対応する `www<N>.a8.net/0.gif` トラッキングピクセルがあり、コードとwww番号の対応を
   必ずgrepで確認してから使う(ズレると計測が壊れる)。
   例外: 松井証券コード `69HAA`(www11)は glossary-shin-nisa.html 専用で他記事に使わない。
7. index.html の「用語解説」ボックス(用語解説記事の場合)にリンク追加、sitemap.xmlに`<url>`追加
8. 公開前チェック: タグの開閉バランス(div/p/h2/a/script/head/html/body)をPythonで数える、
   JSON-LDは `json.loads()` で構文確認
9. 上記コミット規約でcommit + push

## 定期実行タスク(Claude Code Remote scheduled tasks)

- 「罫線帳 株ニュースチェック」(平日18:52 JST): ニュース調査→記事価値判断(重複/新規性)→
  価値ありと判断したら上記チェックリストに沿って記事を自動執筆・公開→報告
- 「罫線帳 2Q決算更新リマインド」(2026/10/28実行予定): 個別株記事33本の2Q決算(4-9月期)を
  IR等で確認し、確定した数値のみ更新。JSON-LDのdateModifiedも更新。コミット・push後、
  更新した/できなかった銘柄を報告。決算は数週間かけて出揃うので部分的な進捗でも良い。

## その他の既知の制約

- WebFetchは度々allowlistでブロックされる(本番サイト含む)。ローカルのgit clone
  (/home/claude/keisen-cho) のファイルを直接読んで確認する方が確実。
- Search Console / AdSenseのデータはAPI接続がないため、ユーザーのスクリーンショット頼み。
