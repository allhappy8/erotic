# パーツプレビュー（1ページ完結 / GitHub Pages）

HTMLパーツのプレビューと共有のための1ファイル完結ページです。
上部に説明ボックス、その下にHTMLソースの表示エリアがあり、
表示エリアの中に **3列の動画ボックス＋各動画のテキスト** を
何段でも並べられます。表示内容はすべてソース内で編集する方式で、
閲覧側から追加・編集はできません。

- 依存パッケージなし（ビルド不要）
- `index.html` 1枚だけで動作
- Google Fonts（Noto Sans JP / JetBrains Mono）のみ外部読み込み

---

## ファイル構成

```
.
├── index.html        ← これ1枚で完結
├── README.md
└── videos/           ← 動画とサムネイルを置くフォルダ
    ├── movie-01.mp4
    ├── movie-02.mp4
    ├── movie-03.mp4
    ├── thumb-01.jpg
    ├── thumb-02.jpg
    └── thumb-03.jpg
```

---

## 公開手順（GitHub Pages）

1. リポジトリの直下に `index.html` を置く
2. GitHub の **Settings → Pages** を開く
3. **Source** を `Deploy from a branch`、Branch を `main` / `(root)` に設定して Save
4. 1〜2分で公開される
   `https://<ユーザー名>.github.io/<リポジトリ名>/`

> 反映されないときは、ブラウザのキャッシュを消すか
> URL の末尾に `?v=2` を付けて読み込み直してください。

---

## 編集する場所

`index.html` の中で触るのは、基本的に次の3か所だけです。

### 1. 説明ボックス（300文字目安）

```html
<p class="note-body" id="noteBody">ここに説明文を書く</p>
```

- 右下に文字数が自動表示されます（空白・改行は除外してカウント）
- 300字を超えるとカウンタの色が変わりますが、**入力制限ではありません**
- 文字数が増えてもボックスの高さが自動で伸びるため、レイアウトは崩れません

### 2. 動画ブロックの中身

`index.html` の `STAGE START` 〜 `STAGE END` の間を編集します。

自前の mp4 を使う場合:

```html
<video src="videos/movie-01.mp4" poster="videos/thumb-01.jpg"
       muted loop playsinline preload="metadata" controls></video>
```

YouTube / YouTube Shorts を使う場合は、`<video>` タグを丸ごと差し替え:

```html
<iframe src="https://www.youtube.com/embed/【動画ID】"
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; picture-in-picture"
        allowfullscreen></iframe>
```

`<video>` でも `<iframe>` でも `<img>` でも、枠にぴったり収まるように
CSS 側で調整済みです。

### 2-2. 動画の下のテキスト

各動画の下のテキストは `<p class="cell-text">` の中身を書き換えます。

```html
<p class="cell-text">ここに表示したいテキスト。<br>改行は br で入れます。</p>
```

閲覧者が入力・編集できる欄ではありません。**すべてソース側で管理します。**

### 2-3. 動画を増やす / 減らす

`<ul class="video-row">` の中の `<li> … </li>` を1つコピーして下に足すだけです。
3つごとに自動で次の段（2段目・3段目…）へ折り返します。
3の倍数でなくても崩れず、4本なら2段目に1本だけ並びます。

```html
<ul class="video-row">
  <li>…動画1…</li>
  <li>…動画2…</li>
  <li>…動画3…</li>
  <!-- ここから2段目 -->
  <li>…動画4…</li>
  ...
</ul>
```

減らすときは `<li> … </li>` を丸ごと削除してください。
CSS の変更は不要です。

### 3. ページタイトル

```html
<title>パーツプレビュー</title>
...
<h1>パーツプレビュー</h1>
<p>ページタイトルはここを書き換えてください。</p>
```

---

## カスタマイズ

CSS 冒頭の `:root` にまとめてあります。

| 変数 | 内容 | 初期値 |
|---|---|---|
| `--video-ratio` | 動画の縦横比 | `9 / 16`（縦動画） |
| `--accent` | アクセントカラー（ボタン・罫線） | `#1F5EFF` |
| `--ink` | 本文の文字色 | `#12192B` |
| `--paper` | ページ背景 | `#F3F5F8` |
| `--card` | カード背景 | `#FFFFFF` |
| `--gap-row` | 段と段の縦の間隔 | `26px` |
| `--gap-col` | 動画どうしの横の間隔 | `10px` |
| `--wrap` | コンテンツの最大幅 | `880px` |

横動画にしたい場合:

```css
--video-ratio: 16 / 9;
```

### 3列を崩したくない理由

`.video-row` は `grid-template-columns: repeat(3, 1fr)` を固定にしてあり、
**スマートフォンでも折り返さず必ず3列**になります。
SPで1列にしたい場合のみ、以下を追記してください。

```css
@media (max-width: 480px){
  .video-row{ grid-template-columns: 1fr; }
}
```

---

## 注意点

- **表示内容はすべてソース管理です。** 閲覧側から追加・編集できる
  UI は置いていません。内容を変えるときは `index.html` を編集して
  コミットし直してください。
- **mp4 をリポジトリに直接置く場合の制限**
  - 1ファイル100MBを超えると push できません
  - GitHub Pages はソフトリミットとして 帯域 約100GB/月・容量 約1GB が案内されています
  - 動画本数が多い、または再生数が見込まれる場合は、
    YouTube の限定公開や Cloudflare R2 などの外部ホストに逃がして
    `<iframe>` で読み込む方が安全です
- `<meta name="robots" content="noindex">` を入れてあります。
  検索結果に出したい場合はこの行を削除してください。
- iOS で動画を自動再生させたい場合は `muted` と `playsinline` の両方が必須です
  （初期状態で付けてあります）。

---

## 動作確認済み

- Safari（iOS）/ Chrome（Android・PC）/ Edge
- `aspect-ratio` を使用しているため、IE11 では動作しません。
