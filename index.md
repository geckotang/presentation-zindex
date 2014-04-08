## z-indexと向き合う

[@GeckoTang](http://twitter.com/GeckoTang)

---

## z-indexとは

- z-indexでは、ボックスの**重なり順序**を指定します。  
- positionで**static以外の値**(relative,absolute,fixed,sticky)が**指定されている要素**に適用されます。  
- 初期値は**auto**で、親と同じ階層になります。
- positionが指定されて、z-indexを指定しない場合は、  
**後から書いたものが上層**に、**先に書いたものが下層**に配置されます。

------

### ちなみにz-indexを指定しない例

```css
div {
	width: 100px;
	height: 100px;
	position: absolute;
	background: rgba(0,0,0,0.5);
}
```

```html
<div style="top:0;  left:0;  background: red;"></div>
<div style="top:1em;  left:1em;  background: blue;"></div>
<div style="top:2em;  left:2em;  background: green;"></div>
```

[結果(fiddle)](http://jsfiddle.net/9HL8K/)

---

## 最近見たz-index

- 10
- 999
- 10000
- 2999999999

---

# 2999999999

にじゅうきゅうおくきゅうせんきゅうじゅうきゅうまんきゅうせんきゅうひゃくきゅうじゅうきゅう

---

# 無駄無駄ァ！！

---

## z-indexには最大値があります！

**2147483647 = 2^31 – 1 = 0×7FFFFFFF**  
で符号付き 32bit 整数の最大値  

※ただし過去にはもっと低い場合こともありました。

------

## 最大値を超える指定をしたら？

- 2147483647と**同じ扱い**になります
- より後方にある要素が上に来ます
- なので最大値以上の値を指定する意味は無い

------

### 最大値を超える指定をしてみた例

```css
div {
	width: 100px;
	height: 100px;
	position: absolute;
	background: rgba(0,0,0,0.5);
}
```

```html
<div style="z-index: 2147483647;  top:0;  left:0;  background: red;"></div>
<div style="z-index: 2999999999;  top:1em;  left:1em;  background: blue;"></div>
<div style="z-index: 2147483647;  top:2em;  left:2em;  background: green;"></div>
```

[結果(fiddle)](http://jsfiddle.net/9HL8K/1/)

---

## なぜ巨大な値を指定する文化が...

<s>とりあえず999なら一番上だよねー</s>

---

## z-indexを理解してないから。

---

## z-indexを理解するには

<br>

### スタックコンテキスト

###スタックレベル

<br>
について知る必要がある。

---

## スタックコンテキスト

- 階層構造を形成する一つの固まり  
- これは、内部にスタックコンテキストを含むことができる  
- その兄弟要素とは完全に独立し、積み重ね処理では、子孫要素だけが考慮される。
- スタックコンテキスト内の要素が、他のスタックコンテキスト内に影響することはない。

<br>

イラレでいうグループみたいなもの

---

## スタックレベル

- z-indexに指定する値
- 同じスタックコンテキストに同じスタックレベルを持つボックスがある場合は、より後方にあるものが前面に来る

---

## スタックコンテキストになるもの

- ルート要素(HTML)
- z-indexプロパティに**auto以外の値**を指定し、positionプロパティにstatic以外の値を指定した要素
- 1未満のopacityを指定した要素

------

### スタックコンテキストを生成する(1)

```css
.z1 { z-index: 1; }
.z2 { z-index: 2; }
.z3 { z-index: 3; }
.z4 { z-index: 4; }
div {
    position: absolute;
    width: 100px;
    height: 100px;
}
```

```html
<div class="z2" style="top: 5em; left: 5em; background: green;">
    <div class="z4" style="top: 1em; left: 1em; background: gold;"></div>
</div>
<div class="z1" style="top: 1em; left: 1em; background: red;">
    <div class="z3" style="top: 1em; left: 1em; background: blue;"></div>
</div>
```

[結果(fiddle)](http://jsfiddle.net/9HL8K/5/)

```
//イメージ
┣.z2
┃┗.z4
┗.z1
　┗.z3
```

------

### スタックコンテキストを生成する(2)

```css
.z1 { z-index: 1; }
.z2 { z-index: 2; }
.z3 { z-index: 3; }
.z4 { z-index: 4; }
div {
    position: absolute;
    width: 100px;
    height: 100px;
}
```

```html
<div class="z2" style="top: 5em; left: 5em; background: green;">
    <div class="z4" style="top: 1em; left: 1em; background: gold;"></div>
</div>
<div style="top: 1em; left: 1em; background: red;">
    <div class="z3" style="top: 1em; left: 1em; background: blue;"></div>
</div>
```

[結果(fiddle)](http://jsfiddle.net/9HL8K/6/)

```
//イメージ
┣.z2
┃┗.z4
┗.z3
```

------

### スタックコンテキストを生成しない

- z-indexにautoを指定したボックスは親のスタックコンテキストの一部と考慮されます。
- 擬似要素を後ろに回り込ませる際に使用しています。

```css
.box {
    position: relative;
    /*z-indexを指定しないが、z-index:auto;扱い*/
    width: 100px; height: 100px;
    background: tomato;
}
.box:before,
.box:after {
    content: "";
    position: absolute;
    z-index: -1;
    bottom: 4px;
    width: 50px; height: 20px;
    box-shadow: 0 2px 10px #000;
}
.box:before {
    left: 10px;
    -webkit-transform: rotate(-5deg);
}
.box:after {
    right: 10px;
    -webkit-transform: rotate(5deg);
}

```

[結果(fiddle)](http://jsfiddle.net/9HL8K/7/)

------

### ↑の例で親にz-indexを指定した場合

- z-indexに数値を指定した場合スタックコンテキストが生まれる
- 擬似要素のスタックレベルを下げても、親要素の背景よりも前に出る

```css
.box {
    position: relative;
    z-index: 1; /*なんかの理由で指定した*/
    width: 100px; height: 100px;
    background: tomato;
}
.box:before,
.box:after {
    content: "";
    position: absolute;
    z-index: -1;
    bottom: 4px;
    width: 50px; height: 20px;
    box-shadow: 0 2px 10px #000;
}
.box:before {
    left: 10px;
    -webkit-transform: rotate(-5deg);
}
.box:after {
    right: 10px;
    -webkit-transform: rotate(5deg);
}

```

[結果(fiddle)](http://jsfiddle.net/9HL8K/8/)

---

## z-index以外でもスタックコンテキストを生成するプロパティ

------

## opacity

- z-indexを指定しなくてもopacityに1未満の値を指定する
- スタックレベルを0としたスタックコンテキストが生成される

------

### opacityがスタックコンテキストを生成する例

```css
.parent { position: relative; }
.icon {
    position: absolute;
    top: -5px; left: -5px;
    width: 50px; height: 50px;
    background: blue;
}
.img {
    width: 100px; height: 100px;
    background: red;
}
.img:hover { opacity: 0.5; }
```

```html
<div class="parent">
    <div class="icon"></div>
    <div class="img"></div>
</div>
```

[結果(fiddle)](http://jsfiddle.net/9HL8K/9/)

------

### ↑の対応

適切なz-indexを指定する

```css
.parent {
    position: relative;
}
.icon {
    position: absolute;
    top: -5px; left: -5px;
    width: 50px; height: 50px;
    background: blue;
    z-index: 1;
}
.img {
    width: 100px; height: 100px;
    background: red;
}
.img:hover {
    opacity: 0.5;
    /*z-index: 0;扱い*/
}
```

[結果(fiddle)](http://jsfiddle.net/9HL8K/10/)

---

## まとめ

- スタックコンテキストの生成条件
- スタックレベルによる重なり順

<br>

を理解すれば  
z-indexに100000とか  
10000000とかを付ける理由がなくなる。

<br>

---

## 参考

- [スタック文脈](https://developer.mozilla.org/ja/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)
- [要素の重なりについて本気出して考えてみた（z-indexとか何とかとか）](http://no1026.com/archives/104)
- [★スタイルシートリファレンス z-index･････重なりの順序を指定する](http://www.htmq.com/style/z-index.shtml)

---

ご清聴ありがとうございました。
