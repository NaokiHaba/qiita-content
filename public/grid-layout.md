---
title: 作って学ぶ　HTML + CSS グリッドレイアウトを読む
tags:
  - CSS
  - grid
private: true
updated_at: '2024-03-11T01:37:22+09:00'
id: d52d0739b31431e24472
organization_url_name: null
slide: false
ignorePublish: false
---

---
title: 作って学ぶ HTML + CSS グリッドレイアウトを読む
tags:

- CSS
- grid
  private: true
  updated_at: '2024-03-10T01:26:56+09:00'
  id: d52d0739b31431e24472
  organization_url_name: null
  slide: false
  ignorePublish: false

---

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

## スタックレイアウト（縦並び・横並び）

- `grid-auto-flow`
    - 横並びに自動配置: `column`
    - 縦並びに自動配置: `row`
- `align-content`・`justify-content` はデフォルトで `stretch` になっている
    - コンテナの高さ・幅に合わせてアイテムが伸ばされてしまう
    - `start` にすることで、コンテナの高さ・幅に合わせず、アイテムの高さ・幅に合わせられる

```html

<ul class="vertical">
	<li><a href="#">Home</a></li>
	<li><a href="#">Service</a></li>
	<li><a href="#">Contact</a></li>
</ul>

<hr/>

<ul class="horizontal">
	<li><a href="#">Home</a></li>
	<li><a href="#">Service</a></li>
	<li><a href="#">Contact</a></li>
</ul>
```

```css
.vertical {
    display: grid;
    gap: 24px;
    align-content: start;
}

.horizontal {
    display: grid;
    grid-auto-flow: column;
    gap: 24px;
    justify-content: start;
}
```

## アイコン付きリンク

### アイコンとテキストを縦並びにする

```html
<a href="#" class="with-icon-vertical">
	<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24" stroke="currentColor"
		 stroke-width="2">
		<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"
		/>
	</svg>
	ホーム
</a>
```

```css
.with-icon-vertical {
    display: grid;
    justify-items: center;
    font-size: 12px;
}
```

### アイコンとテキストを横並びにする

- `grid-template-columns: auto 1fr;`
    - グリッドの列のサイズと数を指定する
    - `auto` はアイコンのサイズに合わせる
    - `1fr` は残りのスペースを埋める
    - `fr` は残りのスペースを均等に分割する

```html
<!-- 横並び（リンク） -->
<a href="#" class="with-icon-horizontal">
	<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24" stroke="currentColor"
		 stroke-width="2">
		<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"
		/>
	</svg>
	ホーム
</a>
```

```css
.with-icon-horizontal {
    display: grid;
    align-items: center;
    gap: 16px;
    grid-template-columns: auto 1fr;
}
```

### 両サイドにアイコンを配置する

```html
<!-- 横並び（両サイドにアイコン） -->
<a href="#" class="with-icon-double">
	<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24" stroke="currentColor"
		 stroke-width="2">
		<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"
		/>
	</svg>
	ホーム
	<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="none" viewBox="0 0 24 24" stroke="currentColor"
		 stroke-width="2">
		<path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7"/>
	</svg>
</a>
```

```css
.with-icon-double {
    display: grid;
    grid-template-columns: auto 1fr auto;
    gap: 16px;
    align-items: center;
}
```

### 横並び（右サイドのアイコンの有無に自動対応）

- `grid-auto-flow: column;`
    - グリッドのアイテムを横並びに自動配置する

```html
<!-- 横並び（右サイドのアイコンの有無に自動対応） -->
<a href="#" class="with-icon-auto">
	<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24" stroke="currentColor"
		 stroke-width="2">
		<path
				stroke-linecap="round"
				stroke-linejoin="round"
				d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"
		/>
	</svg>
	ホーム
</a>
```

```css
.with-icon-auto {
    display: grid;
    grid-template-columns: auto 1fr;
    gap: 16px;
    align-items: center;
    grid-auto-flow: column;
}
```

### 縦並びメニュー（縦並びのアイコン付きリンクを使用）

```html
<!-- 縦並びメニュー（縦並びのアイコン付きリンクを使用） -->
<ul class="menu-vertical">
	<li>
		<a href="#" class="with-icon-vertical">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"
				/>
			</svg>
			ホーム
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-vertical current">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"
				/>
			</svg>
			地図
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-vertical">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"
				/>
				<path stroke-linecap="round" stroke-linejoin="round" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"/>
			</svg>
			ポイント
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-vertical">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"
				/>
				<path stroke-linecap="round" stroke-linejoin="round" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z"/>
			</svg>
			フォト
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-vertical">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"
				/>
			</svg>
			評価
		</a>
	</li>
</ul>
```

```css
.menu-vertical {
    display: grid;
    gap: 16px;
    align-content: start;

    & a {
        padding: 8px 12px;
        border-radius: 8px;

        &.current {
            background-color: #f1f5f9;
        }

        &:hover {
            background-color: #e2e8f0;
        }
    }

    .with-icon-vertical {
        justify-items: center;
        font-size: 12px;
        display: grid;
    }
}
```

### 縦並びメニュー（横並びのアイコン付きリンクを使用）

```html
<!-- 縦並びメニュー（横並びのアイコン付きリンクを使用） -->
<ul class="menu-vertical">
	<li>
		<a href="#" class="with-icon-auto">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"
				/>
			</svg>
			ホーム
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-auto current">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"
				/>
			</svg>
			地図
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-auto">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"
				/>
				<path stroke-linecap="round" stroke-linejoin="round" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"/>
			</svg>
			ポイント
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-auto">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"
				/>
				<path stroke-linecap="round" stroke-linejoin="round" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z"/>
			</svg>
			フォト
		</a>
	</li>
	<li>
		<a href="#" class="with-icon-auto">
			<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" fill="none" viewBox="0 0 24 24"
				 stroke="currentColor" stroke-width="2">
				<path
						stroke-linecap="round"
						stroke-linejoin="round"
						d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"
				/>
			</svg>
			評価
		</a>
	</li>
</ul>
```

```css
.menu-horizontal {
    display: grid;
    grid-auto-columns: 1fr;
    grid-auto-flow: column;
    gap: 2px;
    justify-content: start;

    & a {
        padding: 8px;
        border-radius: 8px;

        &.current {
            background-color: #f1f5f9;
        }

        &:hover {
            background-color: #e2e8f0;
        }
    }

    .with-icon-horizontal {
        display: grid;
        justify-items: center;
        font-size: 12px;
    }
}
```
