---
title: Fuse.jsを使って検索機能を実装する
tags:
  - JavaScript
  - Vue.js
  - fuse.js
private: true
updated_at: '2024-03-09T22:54:17+09:00'
id: dcf045d81f2d7807aa61
organization_url_name: null
slide: false
ignorePublish: false
---


# Fuse.jsとは

Fuse.jsはJavaScriptで書かれた軽量でかつ柔軟な検索ライブラリです。Fuse.jsを使用することにより、Webアプリケーションやモバイルアプリケーションに短時間で高度な検索機能を盛り込むことが可能になります。

## 高速な検索性能

Fuse.jsは極めて高速な検索を実現します。適合スコアリングアルゴリズムを採用しているため、大規模なデータセットに対しても、検索は10ミリ秒未満で実行できます。

具体的には、Fuse.jsを使用したWebアプリケーションの事例では、25,000件の顧客データに対する入力補完検索が100ミリ秒以内で完了し、即時に結果が返されていると報告されています。

## 大規模なデータセットに対応

Fuse.jsは、100万件以上の大規模なデータセットでも検索性能を維持することができます。軽量な実装によりメモリ使用量を抑えるため、リソースの無駄遣いを防ぎながら優れたスケーラビリティを提供します。

## 近似値検索の柔軟性

Fuse.jsは、単なる文字列の完全一致検索だけでなく、スペルミスや入力ミスがあった際でも、関連するデータを見つける近似値検索を可能にします。文字の入れ替えや追加・削除があった場合でも、適切にマッチングできます。

加えて、以下のような高度な検索オプションも提供されています。

- 検索キーのウェイト付け
- 最小文字数の設定
- 近接度の考慮

これらのオプションを組み合わせることで、様々な用途に適した検索ロジックを実装できます。

Fuse.jsを使用すれば、少ない開発リソースでユーザーフレンドリー且つ高いパフォーマンスを持つ検索機能を実現できます。

# Fuse.jsの使い方

実際にVue3アプリケーションにFuse.jsを導入し、検索機能を実装する例を見ていきましょう。

```vue
<script setup lang="ts">
import { computed, ref } from "vue";
import Fuse from "fuse.js";

const searchItems = [
  {
    name: "りんご",
    description:
      "赤や緑、黄色などの色をしている果物です。お菓子や飲み物の材料にもなります。",
  },
  {
    name: "ばなな",
    description:
      "黄色い曲がった形の熱帯果物です。ビタミンCが豊富で栄養価が高い果物です。",
  },
  {
    name: "みかん",
    description:
      "橙色で香り高い柑橘類の果物です。缶詰やジュースにも加工されています。",
  },
  {
    name: "ぶどう",
    description: "小さな実がたくさんついた果物です。ワインの原料にもなります。",
  },
  {
    name: "ストロベリー",
    description:
      "赤い心形の果実で香りが良く、ケーキや飲み物に使われることが多い果物です。",
  },
  {
    name: "オレンジ",
    description:
      "橙色で香り高い柑橘類の果物です。ジュースやお菓子の材料によく使われます。",
  },
  {
    name: "キウイフルーツ",
    description:
      "中は緑色で、毛が生えた不思議な形の果物です。ビタミンCが豊富で栄養価が高い果物です。",
  },
  {
    name: "パイナップル",
    description:
      "鱗片状の表面が特徴的な熱帯果物です。甘みと酸味が調和した味が人気の果物です。",
  },
  {
    name: "ラズベリー",
    description:
      "赤い小さな実がたくさんついた香り高い果実です。ジャムやお菓子の材料によく使われます。",
  },
  {
    name: "ブルーベリー",
    description:
      "小さな青紫色の果実が房になっている果物です。抗酸化作用があり健康に良い果物です。",
  },
];

const fuseOptions = {
  keys: ["title", "description"],
};

const fuse = new Fuse(searchItems, fuseOptions);

const searchQuery = ref("");

const searchResults = computed(() => {
  if (!searchQuery.value) return [];
  return fuse.search(searchQuery.value).map(m => m.item);
});
</script>

<template>
  <main>
    <div class="search-container">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Search..."
        class="search-input"
      />
      <div v-if="searchResults.length" class="search-results">
        <h2>Search Results:</h2>
        <ul>
          <li
            v-for="(result, index) in searchResults"
            :key="index"
            class="search-result"
          >
            <p><strong>Name:</strong> {{ result.name }}</p>
            <p><strong>Description:</strong> {{ result.description }}</p>
          </li>
        </ul>
      </div>
      <div v-else class="no-results">
        <p>No results found</p>
      </div>
    </div>
  </main>
</template>
```

## Fuse.jsのオプション設定

Fuse.jsのオプション設定は、検索対象のキーと検索オプションを指定することで行います。上記の例では、`fuseOptions`に`keys`
プロパティを指定しています。`keys`プロパティには、検索対象のキーを配列で指定します。

### その他のオプション

- `threshold` : マッチングスコアの閾値を設定します。この値を小さくするとマッチングが緩くなり、大きくすると厳しくなります。
- `distance` : 最大編集距離を設定します。編集距離が大きいほど、より多くの文字の置換、削除、追加が許容されます。
- `ignoreLocation` : trueに設定すると、検索語の位置を無視してマッチングを行います。
- `findAllMatches` : trueに設定すると、すべての部分マッチを検索結果に含めます。
- `minMatchCharLength` : マッチングに必要な最小文字数を設定します。
- `shouldSort` : 検索結果をスコア順にソートするかどうかを指定します。

# 検索結果の採点理論

Fuse.jsでは、検索結果のリストを取得した後、各結果アイテムに関連性スコアを付与し、そのスコアに基づいてランク付けを行います。この関連性スコアは、以下の3つの要素から算出されます。

> BitapはByte Approximating Patternの略で、文字列パターンと別の文字列を比較し、パターンが近似的にどの程度一致するかを計算します。完全一致の場合はスコアが最大になり、ミスマッチが多いほどスコアが下がっていきます。

https://tech.pjin.jp/blog/2020/11/13/stringsearch-3

## あいまいさスコア

あいまいさスコアは、検索文字列とマッチした文字列がどの程度似ているかを数値化したものです。Fuse.js内部では、Bitapアルゴリズムの修正版を使ってこのスコアを計算しています。

検索語と完全に一致する場合はあいまいさスコアが最大になり、検索語から離れるほどスコアが下がっていきます。スコアがどの程度下がるかは、以下の2つのオプションで調整できます。

### distance

最大編集距離。この値を大きくすると、より多くの文字の置換・削除・追加を許容します。

### threshold

マッチングと見なすスコアの閾値。この値を小さくするとマッチングが緩くなります。

## キーの重み付け

検索対象のキー(フィールド)ごとに重みを付けることができます。

重みが大きいキーにマッチするほど、関連性が高くなります。デフォルトでは全キーに重み1が設定されています。

## フィールド長ノルム

同じ検索語でも、短いフィールド(タイトルなど)にマッチした方が、長いフィールド(本文など)
にマッチするよりも関連性が高くなります。フィールドが長いほど、関連性スコアは小さくなる傾向があります。

---

このように、Fuse.jsとVue3を組み合わせることで、シンプルかつ高速な検索機能を実装できます。Fuse.jsの検索オプションを調整することで、さらに検索ロジックをカスタマイズすることも可能です。

実際にこの検索機能を動作させてみて、Fuse.jsの曖昧検索の力を体感してみてください!
