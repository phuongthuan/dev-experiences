## Currying in Javascript

Function theo phong cách lập trình theo hướng truyền thống thường nhận vào các tham số, có thể là `String`, `Number`, `Array`, hoặc `Object` sau đó thực thực thi một số logic và cuối cùng return về kết quả.

```javascript
function sum(a, b) {
  return a + b;
}
```
Cao cấp hơn phong cách lập trình theo hướng truyền thống, Function trong Functional Programming nhận vào cả các tham số (arguments) là các function.
```javascript
function sum(a) {
  return (b) => {
    return a + b;
  }
}
```
Function trong JavaScript có khả năng nhận và return về các function khác, điều này dẫn đến sự xuất hiện của rất nhiều các concept:

+ **Pure Function**
+ **Currying**
+ **High-Order Functions (HOC)**

## What is currying in Javascript?
Function trong Javascript thường nhận vào một hoặc nhiều arguments. **Currying** về cơ bản là các funtion nhận vào duy nhất một tham số và bên trong nó return về các function khác. Nghe thì có vẻ hơi khó hiểu nhỉ nên chúng ta hãy đi vào ví dụ thực tế.

Cách viết thông thường:
```javascript
function multiple(x, y, z) {
  return x * y * z;
}

multiple(1, 2, 3); //6
```

**Currying** function:
```javascript
function multiple(x) {
  return function(y) {
    return function(z) {
      return x * y * z;
    }
  }
}

multiple(1)(2)(3); //6
```

Kết quả trả về là như nhau. Thậm chí nhìn thằng Currying hơi bị rối =)). What the heck?? return hell (lol). Thoạt nhìn qua thì thằng Currying chẳng có cái lý do gì thuyết phục để mình sử dụng cả. Nhưng hay nhìn cách call giữa 2 function, chúng ta sẽ thấy khác nhau rõ rệt.

```javascript
multiple(1, 2, 3);

// VS

multiple(1)(2)(3);
```
Có gì đó đặc biệt ở đây, thử viết tách hàm số 2 bên dưới ra xem sao nhé:

```javascript
const mul1 = multiple(1);
const mul2 = mul1(2);
const result = mul2(3);

console.log('mul1', mul1);
console.log('mul2', mul2);
console.log('result', result);
```

Kết quả của lần lượt `mul1`, `mul2`, `result`:

```javascript
"mul1" function(y) {
  return function(z) {
    return x * y * z;
  }
}

"mul2" function(z) {
  return x * y * z;
}

"result" 6
```
Ở trên chúng ta đã tách function `multiple(1)(2)(3)` ra thành:
```javascript
const mul1 = multiple(1);
const mul2 = mul1(2);
const result = mul2(3);
```
Đầu tiên, chúng ta pass `1` vào function `multiple`:
```javascript
const mul1 = multiple(1);
```

Khi này biến `mul1` giữ function bên dưới và nhận vào argument `y`.
```javascript
function(y) {
  return function(z) {
    return x * y * z;
  }
}
```

Tiếp theo pass `2` vào function `mul1`
```javascript
const mul2 = mul1(2);
```
Funtion `mul1` sẽ trả về function cuối cùng và nhận vào argument `z`:

```javascript
function(z) {
  return x * y * z;
}
```

Và cuối cùng khi function `mul2` được gọi, truyền vào argument `z` là `3` chúng ta được kết quả:

```javascript
const result = mul2(3);
console.log(result); // 6
```
Đến đây, cơ bản là chúng ta đã hiểu được luồng chạy nhưng có lẽ là vẫn chưa rõ được cách viết này sẽ mang lại lợi ích gì. Hãy cùng đi đến một ví dụ tiếp theo để hiểu được lợi ích của Currying nào :v 

## Currying is useful?

Giả sử mình là chủ một cửa hàng, và mình muốn giảm giá 10% / tổng hóa đơn cho tất cả các khách hàng thân thiết, khi này mình sẽ viết:

```javascript
function discount(price, discount) {
    return price * discount
}
```

Một hóa đơn có tổng giá trị là 500$, khi giảm giá sẽ còn:

```javascript
const price = discount(500,0.10); // $50 
// $500  - $50 = $450
```

Nếu một ngày có khoảng 100 hóa đơn thì sao nhỉ :)), thì viết lại 100 lần. Oh nhìn cũng đc đó :v
```javascript
const price = discount(1500,0.10); // $150
// $1,500 - $150 = $1,350
const price = discount(2000,0.10); // $200
// $2,000 - $200 = $1,800
const price = discount(50,0.10); // $5
// $50 - $5 = $45
const price = discount(5000,0.10); // $500
// $5,000 - $500 = $4,500
const price = discount(300,0.10); // $30
// $300 - $30 = $270
...

```

Ok đây là lúc mà bạn sẽ nhìn thấy tác dụng thực sự của Currying. Modify hàm discount ban nãy, viết lại theo phong cách currying:

```javascript
function discount(discount) {
    return (price) => {
        return price * discount;
    }
}
const tenPercentDiscount = discount(0.1);
```

Lúc này mình chỉ cần truyền số tiền thôi thay vì truyền cả `discount` + `price` nữa :D

Hoặc nếu tạo một phiếu giảm giá khác, sale up 50% chẳng hạn :v 
```javascript
const fiftyPercentDiscount = discount(0.5);
```

```javascript
fiftyPercentDiscount(500); // 250
// $500 - $250 = $250
fiftyPercentDiscount(5000); // 2500
// $5,000 - $2,500 = $2,500
fiftyPercentDiscount(1000000); // 500000
// $1,000,000 - $500,000 = $500,000
```

## Summary
Khi chúng ta cần truyền vào một biến ít thay đổi, cố định trong hầu hết các trường hợp,nên sử dụng currying.





















