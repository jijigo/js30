# js30#9 - 14 Must Know Dev Tools Tricks

主要介紹google chrome devtools一些使用技巧，除了 `console.log` 外，還有很多很少人知道卻很好用的技巧，快來看看吧

## 查看DOM變化

在做網頁的功能時候，例如有一些DOM的變化可能我們知道是透過js運行的關義，但不知道是哪邊的程式碼造成的
這時候點選那個html element，右鍵選取 `Beark on > attribute modifications`，就能查看是哪裡發生了變化

![](https://i.imgur.com/VtJvGmc.png)

當javascript執行時，devtool會開啟debugger，暫停當前動作並且告訴你是哪段程式碼

![](https://i.imgur.com/pgwPB73.png)


## Console

接下來要介紹很常用的console，是工程師必備技能

### 輸出特定字串

這個就不用介紹了吧，就是在你的js檔裡用 `console.log` 輸出一個字串
待程式執行到那一行時就會輸出到devtool的console中

```
// Regular
console.log('hello');
```

### 輸出字串與變數

有時候我們想透過 `console` 得知變數的內容，這也是很常見的需求。
這邊提到了兩種做法：

第一個是在字串中加入 `%s` ，透過第二個參數給值，類似c++ `printf` 的概念
第二個是使用 `ES6` 的 `Template Literals` 加入變數


```
// Interpolated
console.log('Hello i am a %s string!', '💩')
let fruit = 'apple'
console.log(`I love ${fruit}`)
```

![](https://i.imgur.com/k2ElkW8.png)

### 輸出有樣式的字串

console裡的文字當然也可以有樣式，只需要在輸出的字串前面加入 `%c`，並在第二個參數給他css的樣式就可以了

```
// Styled
console.log(`%c I am a great text`, 'font-size: 50px')
```

![](https://i.imgur.com/1cO2LXr.png)

不過這個我真不知道什麼時候會用到
可能是在自己的網站想嚇嚇其他工程師的時候？ :laughing: [Dcard](https://www.dcard.tw/signup)


![](https://i.imgur.com/3oO5PBH.png)

### 提醒訊息

`warning` 是黃色的， `error` 是紅色的
這個一般蠻常在套件中看到，甚至現在隨便一開某個網站的console都看得到呢

```
// warning!
console.warn('oh noooo')
// Error :|
console.error('shit!')
```

![](https://i.imgur.com/qFPAsX9.png)

### 檢查是否有誤

利用 `console.assert` 可以檢查一段程式碼的結果是否為 `true`，如果為 `false` 就會輸出錯誤

```
// Testing
console.assert(1 === 2, 'You did not select the right Element!')
```

### 清除console

```
// clearing
console.clear();
```

打出這行之後，前面的 `console` 內容都會不見，並留下 `Console was cleared`

![](https://i.imgur.com/yH458Be.png)

如果是要直接在瀏覽器清除，按下左上角的禁止圖示 :no_entry_sign: 也可以全部清除喔

### 查看DOM元素

我們很常會需要把DOM元素印出來看一看
若使用 `console.log` 出來只能看到該元素的html，並看不到整個DOM結構，但透過 `console.dir` 就能方便的查看，是一個很實用的技巧

```
// Viewing DOM Elements
let p = document.querySelector('p');
console.log(p)
console.dir(p)
```

![](https://i.imgur.com/UTxb2TO.png)

### 分群管理

有的時候可能 `console` 的資訊太多會很難閱讀，這時透過 `console.group` 便能把資訊分群
只需要在要分群的前後分別加上 `console.group()` 和 `console.groupEnd()` ，括號中要打上一樣的內容，這個便是分群的名稱

```
// Grouping together
dogs.forEach((dog) => {
    console.group(`${dog.name}`)
    console.log(`This is ${dog.name}`);
    console.log(`${dog.name} is ${dog.age} years old`);
    console.log(`${dog.name} is ${dog.age * 7} dog years old`);
    console.groupEnd(`${dog.name}`)
})
```

沒有使用分群的狀況下，看起來很雜亂

![](https://i.imgur.com/bCuKakA.png)

使用分群的結果，有助於閱讀，而且還能收合

![](https://i.imgur.com/N0ianzB.png)

*Tips*: 若將第一個 `console.group` 改為 `console.group()` ，則預設會是收起來的

### 計算執行次數

有時我們會需要計算某段程式碼執行過幾次，這時用 `console.count` 非常方便，`console.count` 會計算你給它的這個字串已經出現了幾次

```
// counting
console.count('wes')
console.count('wes')
console.count('steve')
console.count('wes')
console.count('wes')
console.count('steve')
console.count('steve')
console.count('steve')
console.count('wes')
console.count('steve')
console.count('wes')
```

![](https://i.imgur.com/XgO8Iji.png)

值得注意的是，如果 `console` 被清除掉， `console.count` 就會重算哦

### 計算執行時間

有時我們會想知道程式運行的時間，只要在執行的開頭和結尾分別加上 `console.time` 和 `console.timeEnd` ，內容輸入一樣的字串，它就會幫你計算這兩個console之間花費了多久時間

```
// timing
console.time('fetching data')
fetch('https://api.github.com/users/wesbos')
    .then(data => data.json())
    .then((data) => {
        console.timeEnd('fetching data')
        console.log(data)
    })
```

![](https://i.imgur.com/HDt4G0W.png)


### 表格

這個是Wesbos補充的，有時候會遇到一個類似資料集的資料，像是下面的範例，有欄位也有筆數
如果用 `console.log` 會比較不直覺，如果用 `console.table` 他便會幫你畫成表格，非常方便

```
// table
const dogs = [{ name: 'Snickers', age: 2 }, { name: 'hugo', age: 8 }];
console.log(dogs)
console.table(dogs)
```

![](https://i.imgur.com/FkmShwy.png)