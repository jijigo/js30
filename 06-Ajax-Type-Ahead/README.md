# js30 06 - Ajax Type Ahead
###### tags: `js30`

![](https://i.imgur.com/jgHu1Zb.gif)

## 目標

在搜尋框輸入字串，搜尋結果可以篩選出字串相符的城市或州名。

## 請求資料

首先，我們要搜尋資料的話，一定要有參考的資料庫或資料集，這些資料通常會是後端或是別人開發出來的API並透過網址取得

```javascript=
const endpoint = 'https://gist.githubusercontent.com/Miserlou/c5cd8364bf9b2420bb29/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json';
```

例如上方的[網址](https://gist.githubusercontent.com/Miserlou/c5cd8364bf9b2420bb29/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json)便是作者提供查詢資料的網址，直接放到用瀏覽器打開會看到很長一串的json資料。

既然資料在外部網址，那我們要怎麼取的呢？

原本javascript是透過`XMLHttpRequest`來請求，看起來會像這樣：

```javascript=
const xhttp = new XMLHttpRequest();
xhttp.onload = () {
  const data = JSON.parse(this.responseText);
  console.log(data)
}
xhttp.onerror = (err) {
  console.log('Fetch Error :-S', err)
}
xhttp.open('get', 'xxx.json', true)
xhttp.send()
```

但由於`XMLHttpRequest`這個API實在太久遠，對於現在的瀏覽器以及複雜的網頁需求已經不敷使用，jQuery框架的出現，把XMLHttpRequest包裝成比較容易使用的方式。

```javascript=
$.ajax({
    // 進行要求的網址(URL)
    url: './sample.json',

    // 要送出的資料 (會被自動轉成查詢字串)
    data: {
        id: 'a001'
    },

    // 要使用的要求method(方法)，POST 或 GET
    type: 'GET',

    // 資料的類型
    dataType : 'json',
})
// 要求成功時要執行的程式碼
// 回應會被傳遞到回調函式的參數
.done(function( json ) {
    $( '<h1>' ).text( json.title ).appendTo( 'body' );
    $( '<div class=\'content\'>').html( json.html).appendTo( 'body' );
  })
  // 要求失敗時要執行的程式碼
  // 狀態碼會被傳遞到回調函式的參數
.fail(function( xhr, status, errorThrown ) {
    console.log( '出現錯誤，無法完成!' )
    console.log( 'Error: ' + errorThrown )
    console.log( 'Status: ' + status )
    console.dir( xhr )
})
// 不論成功或失敗都會執行的回調函式
.always(function( xhr, status ) {
    console.log( '要求已完成!' )    
})
```

最近有了新的Fetch API，主要想取代XMLHttpRequest
fetch是HTML5的API，使用ES6的`promise`，也是這次js30要學習的重點

## Fetch的使用

一個簡單的fetch請求會像這樣子(使用課堂上的網址)

```javascript=
fetch(endpoint)
.then(function(response) {
    return response.json()
})
.then(function(json) {
    console.log('parsed json', json)
})
.catch(function(ex) {
    console.log('parsing failed', ex)
})
```

如果我們把`fetch(endpoint)`console出來會發現他返回的是一個`promise`物件

![](https://i.imgur.com/5JGZsSx.png)

這邊其實就需要理解一下ES6的Promise了
由於Promise要細講篇幅太長了，找時間再發一篇研究Promise的:)
簡單的來說，當promise表示成功（fulfilled）時，用`then()`接收他回傳的值，反之失敗（rejected）時，用`catch()`接收
而fetch, then, catch都是回傳promise物件，所以他們才能串連再一起（chaining）

不信我們來在console出來看看
```javascript=
const prom = fetch(endpoint).then()
console.log(prom)
```
![](https://i.imgur.com/hnl32aT.png)


首先我們來接收成功的回應
```javascript=
fetch(endpoint)
    .then(blob => console.log(blob))
```
![](https://i.imgur.com/laobd1C.png)

Response是成功的，但是卻找不到我們要的資料集，那要如何取得我們要的資料呢
由於我們的資料是`json`格式，打開Response的原型可以看到其中有個`json()`

![](https://i.imgur.com/WLCkdCS.png)

所以我們可以將response經過json的function後，得到的資料在接一個then出來做處理，可以看到這個data就是我們要取得的資料集了

```javascript=
fetch(endpoint)
    .then(blob => blob.json())
    .then(data => console.log(data))
```

那我們要怎麼把資料集存起來以便後續處理呢？
建立一個陣列，把得到的資料丟進去

```javascript=
const cities = [];
etch(endpoint)
    .then(blob => blob.json())
    .then(data => cities.push(...data))
```

## ...data 是何意？

注意到上面程式碼中的這行`cities.push(...data)`，為什麼要`...data`呢？
首先將上方的`cites`console出來，可以看到是長這樣的

![](https://i.imgur.com/ddgS45k.png)

咦？我們的資料怎麼會是存在cites[0]裡面？原來是因為我們用`array.push`的關係。

如果我們直接讓`cities = data`就不會有這個問題（但cities宣告時要用`let`，`const`是不能覆寫的。）

```javascript=
let cities = [];
fetch(endpoint)
    .then(blob => blob.json())
    .then(data => cities = data)
```

假如我們硬要用const宣告呢？如何不讓它存在cites[0]裡？

這時候神奇的`es6`出現了，`...(Ellipsis)`是ES6的新特性，詳細的介紹可以看[這篇](https://eyesofkids.gitbooks.io/javascript-start-from-es6/content/part4/rest_spread.html)。
這邊所使用的是*展開運算符*，簡單的例子如下

```javascript=
const params = [ "hello", true, 7 ]
const other = [ 1, 2, ...params ] // [ 1, 2, "hello", true, 7 ]
```
可以把它想成把`...`後面接的參數複製貼上在陣列裡，要注意的是，兩個都必須是陣列才行
所以`cities.push(...data)`，也就像是`cities = [...data]`的感覺，也就是把data陣列直接複製貼上

## 寫一個function：比對字串

這次主題就是要讓使用者輸入字串，比對後給予相對應的城市或州名。

這個funciton我們讓它取名為`findMatches()`，參數帶入使用者搜尋的字詞，例如findMatches(bos)，搜尋結果出來可能會像是`boston`, `bos...`這樣的結果。
> 不知道為什麼作者的function參數還要帶入cites：`findMatches(bos, cites)`，畢竟cites已經是全域的，應該直接用即可？如果每次跑function都要在帶入一次這麼大的陣列不知道會不會造成什麼負荷XD

既然知道這個function要做什麼，於是乎我先自己寫寫看在看作者的版本。
google搜尋了一下`javascript find string`，似乎很常用的是`indexOf()`和`match()`

用`indexOf()`寫法如下：
```javascript=
function findMatches(wordToMatch) {
    return cities.filter(place => {
        return place.city.indexOf(wordToMatch) > -1 || place.state.indexOf(wordToMatch) > -1
    })
}
```
很遺憾的是，`indexOf()`是有區分大小寫的，這樣的結果不是我們要的。（真的要做的話可能需要先將字串處理成全部大寫（toUpperCase）之類的再進行比對）

作者使用的是`match()`，由於比對的方式使用 [Regex(正規表達式)](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Regular_Expressions)，就能輕易選擇是否區分大小寫。

```javascript=
function findMatches(wordToMatch, cities) {
    return cities.filter(place => {
        const regex = new RegExp(wordToMatch, 'gi');
        return place.city.match(regex) || place.state.match(regex)
    })
}
```

## 取得input輸入的內容

找到比對的資料然後呢？接下來就是轉化成使用者要看到的東西啦。
首先我們先完成和使用者互動的部分：取得input上的字串。

```javascript=
function displayMatches() {
    console.log(this.value);
}
const searchInput = document.querySelector('.search');

searchInput.addEventListener('change', displayMatches)
searchInput.addEventListener('keyup', displayMatches)
```

> 發現作者很喜歡寫具名函式，如果我來寫可能會寫成
> searchInput.addEventListener('change', (e) => {
>    console.log(e.target.value);
> })
> 在想是不是要練習寫具名函式了。

> `change`事件是在滑鼠點擊input以外時觸發，很像`blur`事件，不過`blur`事件不管內容有沒有改變都會觸發。
> 

將displayMatches在改寫一下，把我們要的搜尋結果都存在`matchArray`裡了

```javascript=
function displayMatches() {
    const matchArray = findMatches(this.value)
    console.log(matchArray);
}
```

## 把搜尋結果塞進畫面中！

重要的來了，搜尋結果不該只有我們console出來自己爽。該怎麼把結果放進畫面裡？
這是原來的html：

```htmlmixed=
<ul class="suggestions">
  <li>Filter for a city</li>
  <li>or a state</li>
</ul>
```

可以看到我們主要是將結果一筆一筆以`<li>`方式list出來，

```htmlmixed=
<ul class="suggestions">
  <li>Boston</li>
  <li>Bossier City</li>
    ...
</ul>
```

以往這種情況，我自己本人可能會使用`append()`一筆一筆把資料放進suggestions這個ul裡，但是這種做法每次有變化都要先將suggestions底下的li清空，不然會看到無限的li一直往下延伸...（好可怕）

這種動態資料的方式可能就不適合使用`append()`，作者使用的是`innerHTML`，直接把內容物替換掉，像是醬子：

```javascript=
const suggestions = document.querySelector('.suggestions');
suggestions.innerHTML = '<li>123</li>'
```

另外，我們需要把matchArray取出我們要的資訊，變成一個新的陣列，這時就要用到`map()`，這些陣列我們又需要把他們合在一起，所以最後再加上`join()`

```javascript=
const html = matchArray.map(place => {
    return `<li>
    <span>${place.city}, ${place.state}<span>
    <span>${place.population}</span>
    </li>`
}).join('')
```

最後將這個html塞進suggestions裡
```javascript=
suggestions.innerHTML = html
```

## 如何highlight比對相同的字串

在上面的程式碼中，我們還需要在比對使用者所打的字串，只要是和搜尋相同的字串就要替換成`<li class="hl">String</li>`
想到要替換就馬上想到用[replace](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/String/replace)

```javascript=
str.replace(regexp|substr, newSubstr|function)
```

一樣可以用RegExp，並替換html，真心覺得[Template literals](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Template_literals)好好用

```javascript=
const html = matchArray.map(place => {
    const regex = new RegExp(this.value, 'gi');
    const placeName = `${place.city}, ${place.state}`.replace(regex, `<span class="hl">${this.value}</span>`)
    return `<li>
    <span class="name">${placeName}<span>
    <span class="population">${place.population}</span>
    </li>`
}).join('')
```

![](https://i.imgur.com/Hu16w7M.gif)


## 還沒結束：為人口數加上逗號

我本來也以為結束了，最後的最後，是幫人口數加上逗點。
數字加上逗點其實是非常容易用到的，除了數量，還有金錢等等，很需要學起來。

作者很可愛，直接說是擷取stackoverflow上的[解答](https://stackoverflow.com/questions/2901102/how-to-print-a-number-with-commas-as-thousands-separators-in-javascript)XD(應該是這篇)
當然有現成的解答就直接用囉

```javascript=
const numberWithCommas = (x) => {
  return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
}
```

不過最近有個驚人的新發現，[toLocaleString](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString#Browser_compatibility)能直接加逗點！
```javascript=
var number = 3500;
console.log(number.toLocaleString()); // Displays "3,500" if in U.S. English locale
```

### 自我練習

課堂就講到這裡，不過因為資料集算豐富，還有很多變換的空間
像是作者有提到搜尋的結果可以依照離使用者的遠近做排序（距離的計算）等等，有機會可以實作看看:)

### 參考資料

* [從ES6開始的JavaScript學習生活](https://www.gitbook.com/book/eyesofkids/javascript-start-from-es6/details)
* [從Promise開始的JavaScript異步生活](https://www.gitbook.com/book/eyesofkids/javascript-start-es6-promise/details)
* [使用 fetch 來進行 Ajax 呼叫](https://skychang.github.io/2015/11/02/JavaScript-Use_Javascript_Fetch/)
* [Fetch API — JavaScript 發送 HTTP 請求 (III)](https://blog.jason.party/31/fetch-api)