# js30#08 - Fun with HTML5 Canvas
###### tags: `js30`

![](https://i.imgur.com/rbFmqmz.png)

首圖是醜醜的名字草寫 :satisfied: 

[Canvas](https://developer.mozilla.org/zh-TW/docs/Web/API/Canvas_API/Tutorial)是一個HTML5的新元素，用起來像這樣：

```htmlmixed=
<canvas id="tutorial" width="150" height="150"></canvas>
```

沒錯，就只有一行，因為剩下的都會在javascript裡操作，我們可以想像我們用html設定canvas畫布， 用javascript畫圖的感覺。

![](https://i.imgur.com/IrZithd.gif)


## 開始吧

話不多說，馬上來跟著老師操作

```javascript=
const canvas = document.querySelector('#draw');
const ctx = canvas.getContext('2d');
```

先用id取得畫布，用`getConext()`渲染環境，2d指的就是二維空間，3D則是用[WebGL](https://developer.mozilla.org/zh-TW/docs/Web/API/WebGL_API/Tutorial/Getting_started_with_WebGL)，一般還是多用2d

因為我們這次主題是希望可以把整個螢幕當作畫布來畫畫，所以我們的畫布應該是等於螢幕的長跟寬，雖然我們在canvas的html tag上已經定義了長寬，但用javascript可以重新定義它的長寬

```javascript=
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
```

![](https://i.imgur.com/rp7mBo5.png)

可以看到值已改變
接下來是畫筆的設定：

```javascript=
ctx.strokStyle = '#BADA55';
ctx.lineJoin = 'round';
ctx.lineCap = 'round';
```

[strokStyle](https://www.w3schools.com/tags/canvas_strokestyle.asp) 設定筆的顏色
[lineJoin](https://www.w3schools.com/tags/canvas_linejoin.asp) 設定當兩條線碰在一起相連的形狀
[lineCap](https://www.w3schools.com/tags/canvas_linecap.asp) 設定一條線的頭跟尾的形狀

接下來要想一下何時要觸發繪圖，我們希望是當滑鼠按下去時就開始畫，放開滑鼠時就停止畫圖，因為畫圖是一個狀態，這時我們需要用一個[flag](https://stackoverflow.com/questions/17402125/what-is-a-flag-variable)變數來紀錄是否在畫圖

```javascript=
let isDrawing = false;
canvas.addEventListener('mousedown', () => isDrawing = true);
canvas.addEventListener('mouseup', () => isDrawing = false);
canvas.addEventListener('mouseout', () => isDrawing = false);
```

`isDrawing`就是我們的flag，當滑鼠按下（mousedown）時，狀態改為true，此時就可以開始畫圖，放開時將狀態改為false
還有一個比較常被忽略的`mouseout`，也就是當滑鼠離開視窗，我們也要將狀態改為false

## 畫圖

設定當滑鼠移動時要執行畫圖的動作

```javascript=
canvas.addEventListener('mousemove', draw);
```

在draw函式中，首先判斷isDrawing為false則不執行，接著是執行canvas提供的畫圖指令，都是很直覺的函式

```javascript=
let lastX = 0;
let lastY = 0;

function draw(e) {
    if(!isDrawing) return; // stop running when they are not mouse down
    ctx.beginPath();                     // 創建路徑
    ctx.moveTo(lastX, lastY);           // 開始位置
    ctx.lineTo(e.offsetX, e.offsetY);   // 結束位置
    ctx.stroke();                       // 實際繪製
    lastX = e.offsetX;
    lastY = e.offsetY;
    [lastX, lastY] = [e.offsetX, e.offsetY];    // 將結束位置存起來，下一次move時把這個結束位置當作起始位置，這樣看起來會是連續的
}
```

其中 `lastX = e.offsetX` 和 `lastY = e.offsetY` 可以直接用矩陣的方式如下：

```javascript=
[lastX, lastY] = [e.offsetX, e.offsetY]; 
```

但是上面的函式執行之後會發現，第一次下筆會從(0, 0)開始，且每一次放開滑鼠再重新下筆時，，畫接續上一次結束的點。所以在我們每次下筆的時候，應該要重新設定開始位置為現在滑鼠的位置

```javascript=
canvas.addEventListener('mousedown', (e) => {
    isDrawing = true;
    [lastX, lastY] = [e.offsetX, e.offsetY];
});
```

## 讓畫筆變彩色的

現在畫筆只有一個顏色，我們希望可以畫彩色的應該怎麼做呢
首先想到的就是畫的時候可以改變色碼，除了RGB外，可以使用[HSL](https://developer.mozilla.org/zh-CN/docs/Web/CSS/color_value)，我們只要改變H的值就能改變顏色

> H -- hue 色相
> S -- saturation 飽合度
> L -- lightness 亮度
> 色相為整數的角度值，是從 0 到 360 之間的整數

[Mother effing hsl](http://mothereffinghsl.com/) 是hsl的demo網站

所以我們先建立一個變數hue，也就是用來調整色相的值

```javascript=
let hue = 0; // 色相
```

在開始創建路徑之前，我們重新給予`strokeStyle`，也就是畫筆的顏色，我們將它改為hsl，並加入hue的值

```javascript=
ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`;
ctx.beginPath();                     // 創建路徑
```

因為我們希望顏色不是一直固定的，我們希望色相在畫的過程中可以不斷被增加，所以在draw函式的最後再加上`hue++`

```javascript=
[lastX, lastY] = [e.offsetX, e.offsetY];    // 將結束位置存起來，下一次move時把這個結束位置當作起始位置，這樣看起來會是連續的
hue++;    // 也就是 hue = hue + 1;
```

這樣就完成了彩色的畫筆（這邊有把寬度加粗 `ctx.lineWidth = 100;`）

![](https://i.imgur.com/vpWONOv.gif)

## 改變畫筆的大小

畫筆寬度一直同樣大小看久會很膩，所以我們也要嘗試來改變畫筆的寬度！
作者希望的筆寬是越畫越寬，但到一定的寬度後會越畫越小，小到一定的程度後又要變成越畫越大，所以是一種反彈的感覺

先用一個值來判斷要越畫越大還是越畫越小

```javascript=
let direction = true;
```

在draw函式中，我們加入兩個判斷，第一個是判斷什麼時候要改變方向，可以自己設定不同的範圍區間，第二個是判斷direction為true或false時，比寬要增加或是減少。

```javascript=
if(ctx.lineWidth >=  50 || ctx.lineWidth <= 1) {
    direction = !direction;
}

if(direction) {
    ctx.lineWidth++;
} else {
    ctx.lineWidth--;
}
```

## 還有什麼不同的玩法？

一般我們畫圖如果有重疊的部分會把新的蓋在舊的上，使用 [globalCompositeOperation](https://developer.mozilla.org/zh-TW/docs/Web/API/Canvas_API/Tutorial/Compositing) 可以有不同的變化，像是我加了multiply，重疊的部分會變成顏色相加

```javascript=
ctx.globalCompositeOperation = 'multiply';
```
![](https://i.imgur.com/ERvdA5d.png)

## Bonus - 清除畫布

因為測試過程中每次重畫都要重整覺得好麻煩，於是乾脆做一個清除的按鈕
先簡單設定html和css

*html*
```htmlmixed=
<button id="clear">CLEAR</button>
```

*css*
```css
#clear {
    position: fixed;
    padding: 30px;
    top: 30px;
    left: 50px;
}
```

清除畫布其實只要一行程式:)

```javascript=
const clear = document.querySelector('#clear');

function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);   // 給予清除範圍
}

clear.addEventListener('click', clearCanvas)

```

還有很多可以做的呢，像是上一步、下一步、儲存⋯⋯等等，更進階一點還有讓使用者控制筆畫大小、顏色，有時間再來挑戰 :accept: 


## Reference
* [[CSS3]hsl 及 hsla 顏色](http://abgne.tw/css/css3-lab/css3-hsl-hsla-color.html)