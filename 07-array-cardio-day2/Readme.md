# js30 07 - Array Cardio Day 2
###### tags: `js30`

## 學習目標

這堂課延續Array Cardio Day 1，透過問問題的方式來學習array的應用，可以想成當我們實作碰到困難或需求時，可以如何使用array解決。
這次主要學習的有四種，分別是`some` `every` `find` `findIndex`

有兩個array資料

```javascript=
const people = [
    { name: 'Wes', year: 1988 },
    { name: 'Kait', year: 1986 },
    { name: 'Irv', year: 1970 },
    { name: 'Lux', year: 2015 }
];
const comments = [
    { text: 'Love this!', id: 523423 },
    { text: 'Super good', id: 823423 },
    { text: 'You are the best', id: 2039842 },
    { text: 'Ramen is my fav food ever', id: 123523 },
    { text: 'Nice Nice Nice!', id: 542328 }
];
```

## Some() vs. forEach()

問題：想要找出people陣列中，是否至少一個人19或大於19歲？ （原題目：is at least one person 19 or older?）

首先，我們先了解這個答案要的是什麼，它是一個`true/false`問題，如果答案是是，就表示這個陣列中至少有一個人大於19歲（不管是哪一位），如果答案為否，則表示此陣列沒有人大於19歲

不過，為什麼要用[some()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 而不用[forEach()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)呢？
some跟forEach都是會將陣列一個一個跑過，差別就在於：
```
some只要遇到return true就會停止迴圈
forEach是一定會跑完
```
我們先不管題目來做個比較看看就能看到明顯差異（故意讓每個值true或false都有）

```javascript=
const some = people.some(function(person) {
    if(person.year > 1980) {
        return true
    }
})

const forEach = people.forEach(function(person) {
    if(person.year > 1980) {
        return true
    }
})

console.log(some)    // true
console.log(forEach) // undefined
```

如果把其中的`person.year`也console.log出來還會得到這樣的結果

```javascript=
some 1988
forEach 1988
forEach 1986
forEach 1970
forEach 2015
```

可以看到some只做了一次，forEach做了四次，而且最後一次因為沒有符合條件沒有return true，所以只有undefined的結果。

而針對我們真正的題目，some才是我們要的

接下來跟著作者來實作，像剛剛一樣建置一個function，使用`isAdult`這個命名，表示結果是`true/false`

```javascript=
const isAdult = people.some(function(person) {
    ...
})
```

由於要知道是否有大於19歲，不能直接用year（出生年）判斷，要用今年減掉year才是每個person的年紀。要準確的使用「今年」，不應該直接打數字，使用new Date()可以準確的取得目前的時間，getFullYear()可以取得年份

```javascript=
const currentYear = new Date().getFullYear();   //2018
```

再用一個簡單的判斷

```javascript=
if(currentYear - person.year >= 19) {
    return true
}
```

再把`isAdult` console出來，`{isAdult}`可以連同名稱都包起來變成一個object

```javascript=
console.log(isAdult)
console.log({isAdult}) 
```

![](https://i.imgur.com/Wh9dD1r.png)

不過，剛剛的function實在太冗長、太不ES6了，改成arrow function，以及把不需要的if()拿掉如下，是不是乾淨多了呢：）

```javascript=
const isAdult = people.some(person => {
    const currentYear = new Date().getFullYear();
    return currentYear - person.year >= 19
})
```

其實可以在更簡化成一行呢

```javascript=
const isAdult = people.some(person => (new Date().getFullYear()) - person.year >= 19)
```

> 不過要小提醒一下，簡化及縮短程式碼的行數雖然可以讓程式碼看起來很少、或是看起來很厲害（？），但事實上效能不一定比較高，且要考量易讀性喔
> 

## every()

什麼是`every()`，要怎麼用？
以下是從[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/every)中找到的範例，很淺顯易懂

```javascript=
function isBigEnough(element, index, array) { 
  return element >= 10; 
} 

[12, 5, 8, 130, 44].every(isBigEnough);   // false 
[12, 54, 18, 130, 44].every(isBigEnough); // true
```

看起來跟`some()`類似但是是以false為主，只要有一值為false，就會結束函式，並return結果為false，若全為true則結果為true
似乎很常用來測試，只要有一個選項不對，則測試不通過之類的

```javascript=
const allAdults = people.every(person => (new Date().getFullYear()) - person.year >= 19)
```

其實只要把剛才的函式some改成every就好了，可以看到console結果為false


## find()

在講find之前，要先提到filter。這兩個很像，都會回傳true的陣列，差別在於：

```
filter會回傳所有true的陣列，如果找不到則回傳空陣列
find只會回傳第一個為true的值或物件（不是陣列！），如果找不到則回傳undefined
```

來做個簡單的範例

```javascript=
const foods = [
    { name: 'spaghetti', price: 280 },
    { name: 'soup', price: 60 },
    { name: 'cake', price: 120 },
    { name: 'sandwiches', price: 200 }
];

const filterFoods = foods.filter(function(food) {
    if(food.price < 200) {
        return true
    }
})

const findFoods = foods.find(function(food) {
    if(food.price < 200) {
        return true
    }
})
console.log(filterFoods);
console.log(findFoods);
```

假設我們要找出價錢低於200的食物，分別使用filter和find，一樣的判斷式，來看看結果

![](https://i.imgur.com/lq9EkKL.png)

filter回傳所有true的結果且為陣列，find只回傳第一個true的值/物件。
[codepen:link: ](https://codepen.io/dbjjj/pen/bazVgY?editors=1111) 

回到課堂上的案例，他的問題是：在comments陣列中找到id為823423的comment（原文：find the comment with the ID of 823423）

```javascript=
const comment = comments.find(function(comment) {
    if(comment.id === 823423) {
        return true
    }
})

console.log(comment);
```

可以看到結果為`{text: "Super good"}`
如果這邊使用filter的話姐果會變成`[{text: "Super good"}]`

## findIndex()

問題：在comments陣列中找到id為823423的comment並刪除（原文：Find the comment with this ID ＆ delete the comment with the ID of 823423）

在這個情況中，我們要先找到這個要刪除的id在陣列的第幾個位置，所以酒要用到我們的findIndex()

```javascript=
const index = comments.findIndex(comment => comment.id == 823423) // 1
```

可以看到結果為1，知道他的位置之後，就可以把它刪除

簡單的說一下[splice](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)的用法，其實他可以新增和刪除

```javascript=
var myFish = ['angel', 'clown', 'mandarin', 'sturgeon'];

myFish.splice(2, 0, 'drum'); // 新增字串'drum' 在第index=2的位置
// myFish is ["angel", "clown", "drum", "mandarin", "sturgeon"]

myFish.splice(2, 1); // 移除第二個位置的物件 (也就是字串"drum")
// myFish is ["angel", "clown","mandarin", "sturgeon"]
```

第一個參數放的是index，告訴他你要在陣列哪一個位置開始做刪除
第二個參數deleteCount，表示欲刪除的陣列元素數量的整數，給0就不會刪除，但第三個參數需要告訴他要新增的參數

```javascript=
comments.splice(index, 1)
```
所以我們這樣寫表示刪除從index開始1個值，就可以把我們題目要求的刪掉了

用`console.table`來看結果
![](https://i.imgur.com/zJg5Ys0.png)


如果我們想要保留comments，把刪過的放在newComments裡呢？你可能會想到要這樣做

```javascript=
const newCommets = comments.splice(index, 1);
```
![](https://i.imgur.com/iwn1Mlt.png)

但是得到的結果只有我們被刪除的值的陣列，而不是我們要的刪除後的陣列，那應該怎麼做呢？
用ES6其實很容易:)

```javascript=
const newCommets = [
    ...comments.slice(0, index),
    ...comments.slice(index+1)
];
```

來看看這是怎麼做的
[slice](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)是給它一個起始和結束的位置，他會回傳一個新的陣列給你，如果沒有給予結束的位置，則會從起始位置一直到最後。可以想像是一把剪刀，把你要的其中一段剪下來

那`...`其實在上一篇（補連結）有講到，可以想像是把`...`後面的值複製貼上到目前的位置

這樣新 陣列就完成了。