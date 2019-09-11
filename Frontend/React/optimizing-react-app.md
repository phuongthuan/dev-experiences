## Một số tip để tối ưu hóa performance khi làm việc với React (P1)

Chỉ cần những sai lầm ngớ ngẩn rất đơn giản thôi cũng sẽ làm giảm hiệu suất của ứng dụng React. Muốn app chạy nhanh thì trước tiên phải thực hiện tối ưu cho các phần nhỏ.

## 1. Sử dụng Functional / Stateless Component và PureComponent

Trong React, có tất cả 3 loại Component chính: Functional Component, Class Component và Pure Component.

**Functional Component**: Là một function return JSX. Không có state, hook. Loại component này chỉ được sử dụng để đổ data và hiển thị lên view.

Ưu điểm của loại component này là cực kỳ nhanh do có kích thước nhỏ nhất trong 3 loại Component trên. 

**Class Component**: Là một class với đầy đủ các tính năng, có hàm render return về JSX, constructor để khởi tạo state, sử dụng hook, function, v.v. 

**Pure Component**: Mang trong mình đầy đủ tính năng của Class Component. Tuy nhiên, Component này khác Class Component ở chỗ được custom lại hook `shouldComponentUpdate`. Class Component sẽ được render mỗi khi app chạy, khi chúng ta navigate qua lại giữa các component thì hàm render trong Class Component luôn luôn được gọi. 

Không giống như Class Component, PureComponent chỉ render lại khi có props hoặc state thay đổi.

*Summary: Nên sử dụng Functional / Stateless Component để hiển thị dữ liệu và show lên view. Hạn chế sử dụng Class Component và thay bằng PureComponent.*


## 2. Truyền props Component

Không nên truyền những props quá chung chung, hoặc truyền 1 mớ vào một `Component` như sau:

```js
const Person = (props) => (
    <div {...props}>
        {props.text}
    </div>
)
```

Nên định nghĩa chính xác những property nào mà ta muốn truyền vào, như sau:

```js
const Person = (props) => (
    <div specificAttribute={props.specificAttribute}>
        {props.text}
    </div>
)
```

## 3. Dependency optimization

Việc dùng thư viện của bên thứ 3 là điều gần như là bắt buộc trong phát triển ứng dụng bằng React (vì bản chất React không phải là 1 framework hoàn thiện, React chỉ là 1 thư viện để xây dựng UI mà thôi).

Dùng thư viện bên thứ 3 thì không có gì sai nhưng phải dùng sao cho đúng cách. 

> Chỉ import những gì mà mình sử dụng, không nên import thừa thãi, làm giảm tốc độ load của app

Ví dụ về một thư viện khá phố biến và hầu như ai cũng đã từng sử dụng, đó là `lodash`

Bad

```js
import _ from 'lodash'

const person = {}
_.isEmpty(person) // true
```

Good
```js
import _isEmpty from 'lodash/isEmpty'

const person = {}
_isEmpty(person) // true
```

## 4. Sử dụng ``React.Fragment`` để tránh wrapper nhiều lớp `div`

React chỉ cho phép return về 1 nodo. Do đó, chúng ta thường có thói quen wrap 1 class div bên ngoài. Điều này cũng không sai nhưng không phải tốt nhất, thậm chí còn làm cây DOM của chúng ta trông rất rối 

=> Hãy chuyển sang sử dụng `React.Fragment`

Bad 
```js
const Person = () => (
    <div>
        <p>name</p>
        <p>age</p>
        <p>job</p>
    </div>
)
```


Good 
```js
const Person = () => (
    <React.Fragment>
        <p>name</p>
        <p>age</p>
        <p>job</p>
    </React.Fragment>
)
```

Ở những bản React gần đây thì chúng ta có thể sử dụng cú pháp `<></>` để thay thế cho `<React.Fragment></React.Fragment>`

Extremely Good
```js
const Person = () => (
    <>
        <p>name</p>
        <p>age</p>
        <p>job</p>
    </>
)
```

## 5. Hạn chế những phép tính toán phức tạp trong hàm render() hoặc JSX

Nếu như có trên 3 phép tính toán để hiển thị được kết quả cuối cùng thì chúng ta nên tách ra thành một hàm riêng, tính toán sau đó trả về dữ liệu. 

Bad 
```js
render() {
    const { num1, num2 } = this.props;
    const sum = num1 + num2;
    const max = Max(num1, num2);

    return (
        <div>
            Sum: {sum}
            Max: {Max(num1, num2)}
        </div>
    )
}
```

Good
```js
getSum = () => {
    const { num1, num2 } = this.props;
    return num1 + num2;
}

getMax = () => {
    const { num1, num2 } = this.props;
    return Max(num1, num2);    
}

render() {
    return (
        <div>
            Sum: {this.getSum()}
            Max: {this.getMax()}
        </div>
    )
}
```

## 6. Tránh sử dụng index để làm key trong hàm `map()`

Không nên sử dụng index làm key khi render nhiều `Component` trong function `map()`. Index dùng để xác định các thành phần DOM.

Giả sử, có 10 `Component` được render thông qua functiona `map()`, mỗi `Component` này được gắn `key=index`. Khi này việc push hoặc remove bất kì `Component` nào trong list `Component` trên sẽ làm sai số các `Component`.

Key phải là `unique`, do đó nên sử dụng những thuật toán hoặc các thư viện để generate các đoạn `string` dùng làm `key`. Hoặc nếu dữ liệu có trường `unique` (eg `id`) thì nên sử dụng để làm `key` cho các `Component`.


Bad
```js
persons.map((person, index) => <Person key={index} data={person} />)
```

Good
```js
import shortid from 'shortid'

persons.map(person => <Person key={shortid} data={person} />)
```

Extremly Good
```js
persons.map(person => <Person key={person.id} data={person} />)
```

