---
title: 作って学ぶ　HTML + CSS グリッドレイアウトを読む
tags:
  - 'css'
  - 'grid'
private: true
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# 作って学ぶ　HTML + CSS グリッドレイアウト

## CSS Grid Layoutとは

> CSS Grid Layoutの主要な概念は以下の3つです。
> - Grid Lines (グリッド線)
>
> - Grid Tracks (グリッドトラック = 行トラック・列トラック)
>
> - Grid Areas (グリッドエリア)

```html

<div class="grid">
	<h2 class="item1 boots-logo">Boots Fashion</h2>
	<img class="item2 img-fill" src="../../assets/img/boots.jpg" alt="" width="1980" height="1512"/>
	<img class="item3 img-fill" src="../../assets/img/shirts.jpg" alt="" width="1320" height="1980"/>
	<img class="item4 img-fill" src="../../assets/img/shop.jpg" alt="" width="1600" height="1144"/>
	<img class="item5 img-fill" src="../../assets/img/bag.jpg" alt="" width="1320" height="1980"/>
</div>
```

## Grid Lines (グリッド線)

- 行方向: `grid-template-rows`
- 列方向: `grid-template-columns`

```css
.grid {
    display: grid;
    /*列のライン*/
    grid-template-columns: 20% 20% auto 200px;
    /*行のライン*/
    grid-template-rows: 360px 180px;
    /*ガター（トラックの余白）*/
    gap: 10px;
}

.item1 {
    /*列の配置先*/
    grid-column: 1 / 2;
    /*行の配置先*/
    grid-row: 1 / 2;
}

.item2 {
    grid-column: 2 / 4;
    grid-row: 1 / 2;
}

.item3 {
    grid-column: 4 / 5;
    grid-row: 1 / 3;
}

.item4 {
    grid-column: 1 / 3;
    grid-row: 2 / 3;
}

.item5 {
    grid-column: 3 / 4;
    grid-row: 2 / 3;
}

@media (width <= 768px) {
    .grid {
        grid-template-columns: 20% 20% auto;
        grid-template-rows: 360px 180px 180px;
    }

    .item1 {
        grid-row: 1/-1;
    }

    .item3 {
        grid-column: 2 / 4;
        grid-row: 3 / 4;
    }

    .item4 {
        grid-column: 2 / 3;
    }
}
```

## Grid Tracks (グリッドトラック = 行トラック・列トラック)

- 行トラック: `grid-template-rows` で定義された行
- 列トラック: `grid-template-columns`で定義された列

```css
.grid {
    display: grid;
    /*12分割で利用可能なスペースを1等分とする*/
    grid-template-columns: repeat(12, 3fr);

    /*最初の行を360ピクセルの高さ、2番目の行を180pxの高さに設定*/
    grid-template-rows: 360px 180px;
    gap: 10px;
}

.item1 {
    /*３カラムを<span>にする*/
    grid-column: span 3;
}

.item2 {
    grid-column: span 6;
}

.item3 {
    grid-column: span 3;
    grid-row: span 2;
}

.item4 {
    grid-column: span 5;
}

.item5 {
    grid-column: span 4;
}

@media (width <= 768px) {
    .grid {
        grid-template-rows: 360px 180px 180px;
    }

    .item1 {
        grid-row: span 3;
    }

    .item2 {
        grid-column: span 9;
    }

    .item3 {
        grid-column: span 4;
    }

    .item4 {
        grid-column: span 5;
    }
}
```

## Grid Areas (グリッドエリア)

- `grid-template-areas`プロパティを使用して、グリッドエリアを定義することができます。

```css
.grid {
    display: grid;
    grid-template-areas:
            "text main main side"
            "sub1 sub1 sub2 side";

    grid-template-columns: 20% 20% auto 200px;
    grid-template-rows: 360px 180px;
    gap: 10px;
}

.item1 {
    grid-area: text;
}

.item2 {
    grid-area: main;
}

.item3 {
    grid-area: side;
}

.item4 {
    grid-area: sub1;
}

.item5 {
    grid-area: sub2;
}

@media (width <= 768px) {
    .grid {
        grid-template-areas:
                "text main main"
                "text sub2 side"
                "text sub2 sub1";
        grid-template-columns: 20% 30% auto;
        grid-template-rows: 360px 180px 180px;
    }

}
```