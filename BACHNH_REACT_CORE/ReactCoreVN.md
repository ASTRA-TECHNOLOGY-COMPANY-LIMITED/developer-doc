# React core

> Life cycle of React
> 
> 1. Event handler, normal function.
> 2. setState
> 3. re-render
> 4. useEffect
> 5. …

# I. Interactivity - Tính tương tác

## 1. ****React batches state updates****

- React chờ tới khi mọi Event Handler hoàn thành rồi mới thay đổi State.
- Ví dụ 1
    
    [https://codesandbox.io/s/7os05q?file=/App.js&utm_medium=sandpack](https://codesandbox.io/s/7os05q?file=/App.js&utm_medium=sandpack)
    
    - React sẽ thấy
        
        ```tsx
        setNumber(0 + 1);
        setNumber(0 + 1);
        setNumber(0 + 1);
        ```
        
- Giải pháp 1
    
    [https://codesandbox.io/s/vyqj9q?file=/App.js&utm_medium=sandpack](https://codesandbox.io/s/vyqj9q?file=/App.js&utm_medium=sandpack)
    
    - React sẽ thấy
        
        ```tsx
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        ```
        
        | queued update | n | returns |
        | --- | --- | --- |
        | n => n + 1 | 0 | 0 + 1 = 1 |
        | n => n + 1 | 1 | 1 + 1 = 2 |
        | n => n + 1 | 2 | 2 + 1 = 3 |
        

## 2. Updating Object State

- ****************Mutation:**************** Thay đổi giá trị của chính biến đó ⇒ Không nên sử dụng
    - `const a = array.splice()`
- **Immutation:** Tạo ra một phiên bản copy của biến đó ⇒ Nên sử dụng
    - `const a = array.slice()`
- Ta muốn thay đổi một thuộc tính của Object nào đó mà không thay đổi thuộc tính khác.
    
    ```jsx
    const [person, setPerson] = useState({
        firstName: 'Barbara',
        lastName: 'Hepworth',
        email: 'bhepworth@sculpture.com'
      });
    
    // Sol1
    const newPerson = person;
    newPerson.firstName = "Bach";
    setPerson(newPerson);
    
    // Sol2
    setPerson({
      firstName: "Bach", // New first name from the input
      lastName: person.lastName,
      email: person.email
    });
    
    //Sol3 - Spread syntax (Recommended)
    setPerson({
      ...person, // Copy the old fields
      firstName: "Bach" // But override this one
    });
    ```
    

### Changing nested object

```jsx
const [person, setPerson] = useState({
  name: 'Niki de Saint Phalle',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg', //Change this
    image: 'https://i.imgur.com/Sd1AgUOm.jpg',
  }
});
```

```jsx
setPerson({
  ...person, // Copy other fields
  artwork: { // but replace the artwork
    ...person.artwork, // with the same one
    city: 'New Delhi' // but in New Delhi!
  }
});
```

## 3. Updating Array State

- Updating the Array
    
    
    |  | avoid (mutates the array) | prefer (returns a new array) |
    | --- | --- | --- |
    | adding | push, unshift | concat, [...arr] spread syntax (example) |
    | removing | pop, shift, splice | filter, slice (example) |
    | replacing | splice, arr[i] = ... assignment | map (example) |
    | sorting | reverse, sort | copy the array first (example) |

### Updating objects inside arrays

```jsx
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
];
//...
const [yourList, setYourList] = useState(initialList);

//Wrong
const myNextList = [...myList];
const artwork = myNextList.find(a => a.id === artworkId);
artwork.seen = nextSeen; // Problem: mutates an existing item
setMyList(myNextList);
```

```jsx
//Right
setMyList(myList.map(artwork => {
  if (artwork.id === artworkId) {
    // Create a *new* object with changes
    return { ...artwork, seen: nextSeen };
  } else {
    // No changes
    return artwork;
  }
}));
```

> Sử dụng spread syntax
> 
> - **********Object:********** Sửa hoặc thêm thuộc tính nào đó.
> - **Array:** Thêm phần tử nào đó. Không thể sửa phần tử.

# II. Quản lý State

## 1. Một số quy tắc sử dụng State

### a. **Group related state**

```jsx
const [x, setX] = useState(0);const [y, setY] = useState(0);
```

```jsx
const [position, setPosition] = useState({ x: 0, y: 0 });
```

- Nhận biết
    - Hai State thường thay đổi cùng nhau.
- Ưu điểm
    - Không bị quên khi setState
    - Khi cần thêm thuộc tính (hoặc phần tử đối với mảng) thì không cần khai báo thêm state mới.

### b. **Avoid contradictions in state (state mâu thuẫn)**

```jsx
const [isSent, setIsSent] = useState(false);
const [isSending, setIsSending] = useState(false);
```

```jsx
const [isSent, setIsSent] = useState(false);
//isSending = !isSent
```

- Nhận biết
    - State này chính là nghịch đảo của state kia

### c. **Avoid redundant state (state dư thừa)**

```jsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [fullName, setFullName] = useState('');

function handleFirstNameChange(e) {
  setFirstName(e.target.value);
  setFullName(e.target.value + ' ' + lastName);
}

function handleLastNameChange(e) {
  setLastName(e.target.value);
  setFullName(firstName + ' ' + e.target.value);
}
```

```jsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');

const fullName = firstName + ' ' + lastName;

function handleFirstNameChange(e) {
  setFirstName(e.target.value);
}

function handleLastNameChange(e) {
  setLastName(e.target.value);
}
```

- Nhận biết
    - `fullname` có thể tính toán từ `firstName` và `fullName`
    - fullname không nằm trong useEffect hay async function nào

### d. **Avoid duplication in state - Hay mắc phải**

```jsx
const [items, setItems] = useState(initialItems);
const [selectedItem, setSelectedItem] = useState(items[0]);
```

```jsx
const [items, setItems] = useState(initialItems);
const [selectedId, setSelectedId] = useState(0);
```

- Vấn đề: Khi ta thay đổi `items` thì `selectedItem` sẽ không đồng bộ với nó
    
    [https://codesandbox.io/s/769ixj?file=/App.js&utm_medium=sandpack](https://codesandbox.io/s/769ixj?file=/App.js&utm_medium=sandpack)
    
- Nhận biết
    - Sử dụng state cũ đến khởi tạo cho state mới.

# III. Effect

- Effect thường được sử dụng để đồng bộ hóa với các hệ thống bên ngoài. Chẳng hạn ta sử dụng state để lưu trữ, quản lý một đối tượng từ API bên ngoài.

## 1. Đồng bộ hóa với Effect

### a. Sự khác biệt giữa Effects với Event Handlers

- Thường trong một Component, sẽ có những hoạt động sau
    - Rendering Code: Hiển thị UI, thường không làm những việc phức tạp, Side Effect
    - Event Handlers: Là những hàm nằm bên trong Component, thường làm những SideEffect: Update input field, submit HTTP POST request, navigate to other screen. **Thường được kích hoạt bởi hành động của người dùng.**
- Như vậy vẫn chưa đủ, vì đôi khi ta muốn Trang Web sẽ tự động load dữ liệu khi mở lên, không phải chờ một event nào từ người dùng → **Effect**.
- **Effect giúp ta tạo ra những thay đổi được kích hoạt mà không cần một Event cụ thể.**
- Effect với `[]` trong môi trường lập trình thường được gọi hai lần. Lý do là để lập trình viên có thể xác định xem Effect nào cần cleanup.
    
    [https://codesandbox.io/s/191rb1?file=/App.js&utm_medium=sandpack](https://codesandbox.io/s/191rb1?file=/App.js&utm_medium=sandpack)
    
    [https://codesandbox.io/s/4womvr?file=/App.js&utm_medium=sandpack](https://codesandbox.io/s/4womvr?file=/App.js&utm_medium=sandpack)
    

## 2. Remove unnecessary Effects

- Effect giúp ta đồng bộ với những hệ thống bên ngoài. Nếu không có nhu cầu đó, chỉ cần update State hay Prop thì ta không cần phải sử dụng Effect.
    - Loại bỏ Effect giúp code của ta dễ đọc, chạy nhanh hơn, ít lỗi hơn.
- Thường có 2 trường hợp phổ biến mà ta không cần Effect
    - Không cần sử dụng Effect để thay đổi Data for Rendering
        - Khi ta muốn filter a `list` (là một state) trước khi hiển thị nó, ta thường tạo `stateA` để lưu giá trị sau khi filter, dùng Effect để update `stateA` mỗi khi list thay đổi.
        - Việc này khiến chương trình trở nên chậm chạp vì
            - Khi ta update `list`, React sẽ re-render
            - Sau đó mới gọi tới Effect, Effect sẽ update `stateA`
            - Rồi sẽ lại re-render
        - Giải pháp: thay vì tạo `stateA` để lưu giá trị sau khi filter, ta chỉ cần tạo một biến bình thường để lưu `const normalA = filter(list);`, biến này sẽ được tính toán lại mỗi khi `list` thay đổi.
    - Không cần sử dụng Effect để handle user event
        
        For example, let’s say you want to send an `/api/buy` POST request and show a notification when the user buys a product. In the Buy button click event handler, you know exactly what happened. By the time an Effect runs, you don’t know *what* the user did (for example, which button was clicked). This is why you’ll usually handle user events in the corresponding event handlers.
        
- Sau đây là một số trường hợp mà ta có thể loại bỏ useEffect()

### a. Updating state ****based on props or state****

```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  const [fullName, setFullName] = useState('');

  // 🔴 Avoid: redundant state and unnecessary Effect
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```

```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

### b. ****Resetting all state when a prop changes****

- Tình huống: Ta sử dụng Zalo, khi lựa chọn người khác để gửi tin nhắn, tin nhắn đã soạn cần được reset.

```jsx
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId} //Add key
    />
  );
}

function ProfilePage({ userId }) {
  const [message, setMessage] = useState('');
  // 🔴 Avoid: Resetting state on prop change in an Effect
  useEffect(() => {
    setMessage('');
  }, [userId]);
  // ...
}
```

```jsx
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId} //Add key
    />
  );
}

function Profile({ userId }) {
  // ✅ This and any other state below will reset on key change automatically
  const [message, setMessage] = useState('');
  // ...
}
```

### c. Initializing the application

```jsx
function App() {
  // 🔴 Avoid: Effects with logic that should only ever run once
  useEffect(() => {
    loadDataFromLocalStorage();
    checkAuthToken();
  }, []);
  // ...
}
```

```jsx
let didInit = false;
function App() {
  useEffect(() => {
    if (!didInit) {
      didInit = true;
      // ✅ Only runs once per app load
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  }, []);
}
```

> Effect với `[]` trong môi trường lập trình sẽ được gọi hai lần. Lý do là để lập trình viên có thể xác định xem Effect nào cần cleanup.
>