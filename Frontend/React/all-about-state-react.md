# All you need to know about `state` in React

## State overview

Khi làm việc với React, có 2 concept được coi là xương sống của thư viện này. Bên cạnh những `Component`, Life Cycle Hook. Đó là `state` và `props`. 

`state` tồn tại trong `Component` một cách private. Mỗi `Component` tự quản lý `state` của chính nó, không chỉ sẻ `state` với các `Component` khác.

`state` được khởi tạo thông qua hàm `constructor` trong `Component`. 

```js
class Component extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    }
  }
}
```

## Updating state

> Important: Chỉ có thể gán giá trị trực tiếp cho state (this.state = ...) ở `Component` `constructor`. Còn lại tất cả những nơi khác, phải sử dụng hàm `setState`.

Hàm `setState` nhận giá trị đầu vào là một Object. Thay đổi chúng sau đó merged với current state hiện tại. 

Mặc dù chúng ta vẫn có thể thay đổi state bằng cách viết trực tiếp. Nhưng làm cách này, khi giá trị thay đổi, `Component` sẽ không tự re-render. Và quan trọng hơn, giá trị `state` sẽ không nhất quán. 

```js
// Wrong
this.state.count = 2;

// Correct
this.setState({
  count: 2
});
```

## setState is asynchronous
![alt text](https://miro.medium.com/max/700/1*8C-lQyM_xrp4QOIFjXcA2A@2x.jpeg)

> State Updates May Be Asynchronous. React may batch multiple `setState()` calls into a single update for performance.

Vì một vài lý do về batch update performance, `state` không được thay đổi ngay tức khắc sau khi hàm `setState` được gọi. 

Vâng, điều đó có nghĩa là chúng ta không thể call `setState` ở dòng đầu tiên và hi vọng nó thay đổi ở dòng tiếp theo :v.

```js
// assuming this.state.name = ''
this.setState({
  name: 'Thuan'
});

console.log(this.state.name); // Noooooooope :)
```

React sẽ gộp tất cả những hàm `setState` sau đó gọi 1 lần duy nhất, để tránh việc render lại không cần thiết, làm tăng performance  của app. 

```js
// assuming this.state.count === 0
this.setState({count: this.state.count + 1});
this.setState({count: this.state.count + 2});
// this.state.count === 2, not 3
```

Ở ví dụ trên, chúng ta đều hi vọng rằng lần setState đầu tiên, `count` sẽ mang giá trị `1`. Tiếp theo ở lần setState thứ hai, `count` sẽ mang giá trị `3`. Nhưng `setState` là asynchronous, do đó những hàm `setState` được gọi sau cùng sẽ overwrite lên hàm `setState` trước đó.


## setState accepts a function as its parameter

Ngoài cách truyền vào 1 Object, chúng ta có thể truyền 1 function vào hàm `setState`, function này nhận vào 2 tham số là previous `state` và `props` tại thời điểm các thay đổi đc cập nhật. Điều này đảm bảo rằng giá trị được update cuối cùng luôn dựa trên previous `state`.

```js
// assuming this.state.count === 0
this.setState((prevState, props) => ({ count: prevState.count + 1 }));
this.setState((prevState, props) => ({ count: prevState.count + 2 }));
// this.state.count === 3
```

> Important: Luôn luôn sử dụng cú pháp này khi update state dựa trên giá trị của state trước đó (previous state)

## setState accepts a callback

Hàm `setState` nhận vào 2 tham số, `updater` và `callback`. Tham số thứ hai, `callback` sẽ được thực thi ngay sau khi state được update. 

```js
// assuming this.state.name = ''
this.setState({
  name: 'Thuan'
}, () => console.log(this.state.name));
```

## Common mistakes

Một trong những sai lầm kinh điển khi sử dụng Component state là việc init giá trị khởi tạo của `state` dựa trên `props` trong hàm `constructor`. 

Example:

```js
class Component extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: this.props.count };
  }
  
  render() {
    return <div>The count is: {this.state.count}</div>
  }
}
```
Nếu parent `Component` render:
```js
<Component count={10} />
```

Khi đó trong hàm render của `Component` sẽ hiển thị "The count is 10". Nhưng nếu parent thay đổi thành:

```js
<Component count={20} />
```
Thì trong `Component` vẫn sẽ hiển thị "The count is 10" do React không `Component` không tự destroy và recreate, `constructor` chỉ được init 1 lần duy nhất. Để giải quyết vấn đề này chúng ta nên quan sát những thay đổi của `props` và sau đó update lại sử dụng life cycle hook `componentWillReceiveProps`.

```js
class Component extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: this.props.count };
  }
  componentWillReceiveProps(nextProps) {
    if(nextProps.count !== this.props.count) {
      this.setState({ count: nextProps.count });
    }
  }
  render() {
    return <div>The count is: {this.state.count}</div>
  }
}
```

Ở những phiên bản trong tương lai, life cycle `componentWillReceiveProps` sẽ bị loại bỏ và thay vào đó là `getDerivedStateFromProps`. 

```js
// Return giá trị state sẽ bị thay đổi khi prop thay đổi
static getDerivedStateFromProps(nextProps, prevState){
   if (nextProps.count !== prevState.count){
     return { count: nextProps.count };
  }
  else return null;
}

// set lại state
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
      this.setState({count: prevProps.count});
  }
}

```
