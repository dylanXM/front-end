## 数组API

### 直接修改数组的API

`pop()`：删除数组的最后一个元素，并且返回这个元素

`push()`：添加一个或者多个元素到数组的末尾，并返回数组新的长度

`shift()`：删除数组的第一个元素，并返回这个元素；如果数组为空返回`undefinded`

`unshift()`：在数组开始处插入一些元素，并返回数组新的长度

`splice()`：在数组中删除、插入和替换一些元素，并返回一个包含从原数组中删除的项的数组，若没有删除任何项返回空数组

```js
Array.splice(index, length, ...nums?);
```

- 删除：指定两个参数，如`splice(0,2)`会删除数组的前两项
- 插入：指定三个参数，如`splice(2,0,4,6)`会从当前数组位置2开始插入4和6
- 替换：指定三个参数，如`splice(2,1,4,6)`会删除位置2的元素，然后再从位置2插入4和6

`reverse()`：颠倒数组中元素的顺序

`sort()`：用于对数组中的元素进行排序，在排序时会调用每个数组项的`toString()`转型方法，然后比较得到的字符串

`fill()`：将数组指定区间内的元素替换为某个值

`copyWithin()`：数组内元素之间的替换；参数有三个，①被替换的起始位置，②选取替换值的起始位置，③选取替换值的结束位置

```js
var arr = [1, 'c', 'd', 'a', 'b'];   // 将'a', 'b'替换到'1'的位置
arr.copyWithin(0, 3, 5);             // ['a', 'b', 'd', 'a', 'b']
```

### 返回新数组的API

`concat()`：将参数添加到原数组中。这个方法会先创建当前数组的一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。在没有给`concat()`方法传递参数的情况下，它只是复制当前数组并返回副本

```js
var arr = [1, 3, 5, 7];
var arrCopy = arr.concat(9, [11, 13]);
console.log(arrCopy);   // [1, 3, 5, 7, 9, 11, 13]
console.log(arr);       // [1, 3, 5, 7](原数组未被修改)
```

`slice()`：返回从原数组中指定开始下标到指定结束下标之间的项组成的新数组--不包括结束项

`join(separator)`：将数组元素组成一个字符串，以`separator`为分隔符（不改变原数组）

`indexOf()`：用于查找元素在数组中第一次出现时的索引，如果没有，返回-1

`lastIndexOf()`：用于查找元素在数组最后一次出现时的索引，如果没有，返回-1

`includes()`：用来判断当前数组是否包含某个指定的值，如果是组返回true，否则返回false

`toSource()`：返回数组的源代码，目前只有firefox实现了它

### 数组遍历方法

`forEach()`：指定数组的每项元素都执行一次传入的函数，返回值为`undefined`。默认有传参，参数分别为：遍历的数组项，对应的数组索引，数组本身

```js
var arr = [1,2,3];
arr.forEach(function(item, index, array){
   console.log(item + '-' + index + '-' + (array === arr));
});
// 输出为：
// 1-0-true
// 2-1-true
// 3-2-true
```

`map()`：遍历数组，使用传入函数处理每一个元素，并返回函数的返回值组成的新数组

```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.map(item => item*item);
console.log(arr2); //[1, 4, 9, 16, 25]
```

`filter()`： “过滤”功能，数组中的每一项运行给定函数，返回满足过滤条件组成的数组。 

```js
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
var arr2 = arr.filter((item, index) => {
　　return index % 3 === 0 || item >= 8;
}); 
console.log(arr2); //[1, 4, 7, 8, 9, 10]
```

`every()`： 判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。 

```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.every(x => x<10); 
console.log(arr2); //true
var arr3 = arr.every(x => x<3); 
console.log(arr3); // false
```

`some()`： 判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。 

```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.some(x => x<3); 
console.log(arr2); //true
var arr3 = arr.some(x => x<1); 
console.log(arr3); // false
```

` reduce()+reduceRight()`：这两个方法都会实现迭代数组的所有项，然后构建一个最终返回的值。reduce()方法从数组的第一项开始，逐个遍历到最后。而 reduceRight()则从数组的最后一项开始，向前遍历到第一项。

这两个方法都接收两个参数：一个在每一项上调用的函数和（可选的）作为归并基础的初始值。

传给 reduce()和 reduceRight()的函数接收 4 个参数：前一个值、当前值、项的索引和数组对象。这个函数返回的任何值都会作为第一个参数自动传给下一项。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数就是数组的第二项。

下面代码用reduce()实现数组求和，数组一开始加了一个初始值10。

```js
var values = [1,2,3,4,5];
var sum = values.reduceRight((prev, cur, index, array) => {
    return prev + cur;
},10);
console.log(sum); //25
```

` find()+findIndex() `： 返回数组中第一个符合条件的元素，findIndex返回索引 

```js
[1, 2, 3, 4, 5].find((item) => {return item > 3}) //4
```

` entries()/keys()/values() `： 都取/取键/取值 

```js
var arr=['a', 'b', 'c']
for(let key of arr.keys()){console.log(key)} //0,1,2                    
for(let value of arr.values()){console.log(value)} //a,b,c               
for(let [key, value] of arr.entries()){console.log([key,value])} //[0,'a'],[1,'b'],[2,'c']
```

`includes()`： 判断数组是否包含某项，返回true/false 

```js
[1, 2, 3, 4, 5].includes(4)    //true
[1, 2, 3, 4, NaN].includes(NaN)    //true
```

## String API







