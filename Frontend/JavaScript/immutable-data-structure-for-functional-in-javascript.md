## Immutable Data Structures for Functional in JavaScript

## What is Mutability and Immutability?

> *In object-oriented and functional programming, an immutable object (unchangeable object) is an object whose state cannot be modified after it is created. This is in contrast to a mutable object (changeable object), which can be modified after it is created.*


Hiểu một cách nôm na là *Mutability* có thể thay đổi giá sau khi đã khởi tạo, còn *Immutability* thì không.


Trong JavaScript, **tất cả các kiểu dữ liệu nguyên thủy (primitive) đều là immutability.**

Ví dụ dưới đây là **Immutability**

👇🏻
```javascript
Strings
---
let s1 = 'Hello World!';

// Cắt 5 phần tử đầu của chuỗi s1
let s2 = s1.splice(0, 5); 

console.log(s1); // Hello World! 👉🏻 chuỗi s1 vẫn không bị thay đổi
console.log(s2); // Hello 

Numbers
---
let x = 10;

// Assign y = x, lúc này y có giá trị là 10
let y = x;

// Gán cho y một giá trị mới = 5
y = 5;

console.log(x); // 10 👉🏻 x vẫn giữ nguyên giá trị cũ, không bị thay đổi theo y mặc dù y đã thay đổi
console.log(y); // 5
```

Do đó có thể kết luận rằng, mỗi khi chúng ta thay đổi dữ liệu, nó sẽ tạo ra một instance mới hoàn toàn và không ảnh hưởng tới instance cũ.


Không giống như primitive type, **`Object` và `Array` trong JavaScript mặc định đã được *mutate***.

👇🏻

```javascript
const 🦆 = {
  legs: 2
};

const 🐕 = 🦆;

🐕.legs = 4;

console.log(🐕); // 4 legs
console.log(🦆); // 4 legs

console.log(🐕===🦆); // true

```

Nhưng chúng ta có thể immutability bằng cách sau:

```javascript
const 🦆 = {
  legs: 2
};

const 🐕 = Object.assign({}, 🦆); // Clone

🐕.legs = 4;

console.log(🐕); // 4 legs
console.log(🦆); // 2 legs

console.log(🐕===🦆); // false

```
Immutability sử dụng một state duy nhất, không ảnh hưởng (no side effect) đến các state khác trong ứng dụng. Điều này mang lại rất nhiều benefit như dễ dàng cho việc test, state management.


## Disadvantages of Mutability and Immutability
---

Mutability: Mutate Object tạo ra side effect dẫn tới nhiều bugs không mong muốn.

Immutability: Immutability là rất tốt, nó tránh được nhiều bugs nhưng vô hình chung lại làm giảm performance của app. Immutability tạo ra một bản sao giống hoàn toàn so với bản gốc, sau đó edit dữ liệu mà chúng ta muốn thay đổi trên bản sao này. Điều có có nghĩa là nó sẽ tốn rất nhiều memory cho việc copy các `Object` hoặc `Array`. Thử tưởng tượng chúng ta muốn thay đổi 1 giá trị trong một Array bao gồm 1 triệu phần tử thì sẽ tốn nhiều memory như thế nào nhỉ 🤔


## The Solution
---

Để giải quyết được vấn đề memory lead. Immutable sử dụng 1 cấu trúc dữ liệu có lên là **"trie data structures"**. Cấu trúc dữ liệu này sử dụng một concept là **Structure Sharing** (tối ưu memory bằng cách tái sử dụng). 

Theo cách Immutability thông thường, mỗi khi thay đổi một thuộc tính nào đó, chúng ta phải clone toàn bộ `Object` hoặc `Array` thành một bản sao, sau đó thực hiện modify trên chính bản sao đó. 

Example:

```
zoo = [🐕, 🐈, 🐍, 🐖, 🐇, 🦅]

Thực hiện thay đổi 🐍 => 👽

👉🏻 Clone zoo, sau đó thưc hiện thay đổi, chúng ta được một collection mới:

newZoo = [🐕, 🐈, 👽, 🐖, 🐇, 🦅]

Nếu bạn để ý thì 2 zoo và newZoo chỉ khác nhau 1 phần tử là 🐍 và 👽

```

Còn nếu sử dụng theo Structure Sharing thì nó sẽ như thế này:
```
                           zoo                newZoo
                          /   \                 | 
                        /\     /\               |
                      /   \   /   \             |
                    O1     02      03           |
                    /\     /\      /\           /\
                   🐕🐈   🐍🐖   🐇🦅         👽🐖
```
newZoo tái sử dụng các root `01`, `03`, chỉ modify duy nhất root `02` và thay thế 🐍 => 👽

Lúc này zoo và newZoo là 2 collection hoàn toàn độc lập và tách biệt. Chúng ta có thể thấy structure data theo cách này tiết kiệm đc rất nhiều memory. 

Ý tưởng và cấu trúc thì đã rõ ràng, vậy thì làm sao chúng ta có thể implement vào code? 

Thật may mắn là hiện tại có khá nhiều thư viện hỗ trợ chúng ta thực hiện công việc này. [Immutablejs](https://github.com/immutable-js/immutable-js) và [mori](https://github.com/swannodette/mori) là 2 thư viện phổ biến nhất implement immutability sử dụng cấu trúc Structure Sharing. 


Ví dụ về Immutablejs:
```javascript
import Immutable, { set } from "immutable";

const list = Immutable.List([1, 2, 3, 4]);

// Get second element
const secondElement = list.get(1);
console.log(secondElement); // 2

// Update List
const updatedList = set(list, 2, 100);

console.log(updatedList.toJS()); // [1, 2, 100, 4]
console.log(list.toJS()); // [1, 2, 3, 4]
```

## Conclusion

Recap

```
Mutability: 😣

Immutability: 😍

Copying: 🐌 &  🐘

Sharing: 🚀
```