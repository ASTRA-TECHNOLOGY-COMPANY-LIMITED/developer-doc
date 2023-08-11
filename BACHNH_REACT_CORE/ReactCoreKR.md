# React core - KR

> Life cycle of React
> 
> 1. Event handler, normal function.
> 2. setState
> 3. re-render
> 4. useEffect
> 5. …

# **I. 상호작용 - Tính tương tác**

## **1. 리액트 상태 업데이트 배치**

- 리액트는 모든 이벤트 핸들러가 완료될 때까지 상태를 변경하지 않습니다.
- 예제 1
    
    **[https://codesandbox.io/s/7os05q?file=/App.js&utm_medium=sandpack](https://codesandbox.io/s/7os05q?file=/App.js&utm_medium=sandpack)**
    
    - 리액트가 감지할 내용
        
        ```
        tsxCopy code
        setNumber(0 + 1);
        setNumber(0 + 1);
        setNumber(0 + 1);
        ```
        
- 해결 방법 1
    
    **[https://codesandbox.io/s/vyqj9q?file=/App.js&utm_medium=sandpack](https://codesandbox.io/s/vyqj9q?file=/App.js&utm_medium=sandpack)**
    
    - 리액트가 감지할 내용
        
        ```
        tsxCopy code
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        ```
        
        | 대기 중인 업데이트 | n | 반환값 |
        | --- | --- | --- |
        | n => n + 1 | 0 | 0 + 1 = 1 |
        | n => n + 1 | 1 | 1 + 1 = 2 |
        | n => n + 1 | 2 | 2 + 1 = 3 |

## **2. 객체 상태 업데이트**

- **변이:** 변수 자체의 값을 변경 ⇒ 사용하지 마십시오
    - **`const a = array.splice()`**
- **불변성:** 변수의 복사본을 만듭니다 ⇒ 사용해야합니다
    - **`const a = array.slice()`**
- 다른 속성은 변경하지 않고 특정 객체의 속성을 변경하려는 경우
    
    ```jsx
    
    const [person, setPerson] = useState({
        firstName: 'Barbara',
        lastName: 'Hepworth',
        email: 'bhepworth@sculpture.com'
      });
    
    // 해결 방법 1
    const newPerson = person;
    newPerson.firstName = "Bach";
    setPerson(newPerson);
    
    // 해결 방법 2
    setPerson({
      firstName: "Bach", // 입력 필드의 새로운 이름
      lastName: person.lastName,
      email: person.email
    });
    
    // 해결 방법 3 - 전개 구문 (권장)
    setPerson({
      ...person, // 이전 필드를 복사
      firstName: "Bach" // 하지만 이 필드는 재정의
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

> 스프레드 구문 사용
> 
> - **객체(Object):** 특정 속성 수정 또는 추가.
> - **배열(Array):** 특정 요소 추가. 요소는 수정할 수 없습니다.

## II. Managing State

### 1. Rules when using State

### a. **Group related state**

```jsx
const [x, setX] = useState(0);const [y, setY] = useState(0);
```

```jsx
const [position, setPosition] = useState({ x: 0, y: 0 });
```

- 식별
    - 두 개의 상태(State)는 일반적으로 함께 변경됩니다.
- 장점
    - setState 할 때 잊어버리지 않습니다.
    - 속성(또는 배열의 요소)을 추가해야 할 경우 새로운 상태를 선언할 필요가 없습니다.

### b. **Avoid contradictions in state (상호 충돌하는 상태(State) 사용을 피하십시오.)**

```jsx
const [isSent, setIsSent] = useState(false);
const [isSending, setIsSending] = useState(false);
```

```jsx
const [isSent, setIsSent] = useState(false);
//isSending = !isSent
```

- 식별
    - 이 상태(State)는 다른 상태의 반대입니다.

### c. **Avoid redundant state (중복된 상태)**

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

식별

- **`fullname`**은 **`firstName`**과 **`lastName`**으로 계산될 수 있습니다.
- fullname은 useEffect나 비동기 함수 내에 있지 않습니다.

### d. **Avoid duplication in state (자주 발생하는 실수)**

```jsx
const [items, setItems] = useState(initialItems);
const [selectedItem, setSelectedItem] = useState(items[0]);
```

```jsx
const [items, setItems] = useState(initialItems);
const [selectedId, setSelectedId] = useState(0);
```

- 문제: items을 변경할 때 selectedItem이 동기화되지 않습니다.
- 식별
    - 새로운 상태를 초기화할 때 이전 상태를 사용합니다.

# III. Effect

- 효과(Effect)는 주로 외부 시스템과의 동기화를 위해 사용됩니다. 예를 들어, 우리는 상태(state)를 사용하여 외부 API로부터 객체를 저장하고 관리합니다.

## **1. Effect와의 동기화**

### **a. Effect와 이벤트 핸들러의 차이점**

- 일반적으로 컴포넌트 내에서 다음과 같은 활동이 있습니다.
    - 렌더링 코드: UI를 표시하며 일반적으로 복잡한 작업이나 부작용을 수행하지 않습니다.
    - 이벤트 핸들러: 컴포넌트 내부에 있는 함수로, 주로 부작용을 수행합니다. 입력 필드 업데이트, HTTP POST 요청 제출, 다른 화면으로 이동 등. **일반적으로 사용자의 조작에 의해 트리거됩니다.**
- 그러나 여기에는 아직 충분하지 않습니다. 때로는 웹 페이지가 자동으로 데이터를 로드하도록 하려고 합니다. 특정 이벤트를 기다리지 않아도 되는 경우가 있습니다. 이를 위해 **효과(Effect)**를 사용합니다.
- **효과(Effect)는 특정 이벤트 없이도 트리거될 수 있는 변경사항을 만들 수 있습니다.**
- Effect에서 **`[]`** (빈 배열)을 사용하는 경우, 프로그래밍 환경에서 주로 두 번 호출됩니다. 이는 프로그래머가 어떤 Effect가 정리(clean-up)가 필요한지를 결정할 수 있도록 하기 위함입니다.

## 2. Remove unnecessary Effects

- 효과(Effect)는 외부 시스템과의 동기화를 도와줍니다. 그러나 동기화가 필요하지 않고 상태(State)나 속성(Prop)만 업데이트해야하는 경우에는 효과를 사용할 필요가 없습니다.
    - 효과를 제거하면 코드가 더 읽기 쉽고 실행 속도가 빨라지며 오류가 줄어듭니다.
- 일반적으로 효과를 사용하지 않아도 되는 두 가지 일반적인 경우가 있습니다.
    - 렌더링을 위한 데이터 변경에 효과를 사용할 필요가 없는 경우
        - 예를 들어, 리스트(**`list`**라는 상태)를 필터링한 후에 표시하고 싶을 때, 보통은 **`stateA`**를 만들어 필터링 후의 값들을 저장하고, 효과를 사용하여 **`list`**가 변경될 때마다 **`stateA`**를 업데이트합니다.
        - 이렇게 하면 프로그램이 느려지는 문제가 발생합니다.
            - **`list`**를 업데이트하면 React가 다시 렌더링합니다.
            - 그 후에 효과가 호출되고, 효과가 **`stateA`**를 업데이트합니다.
            - 그 다음 다시 렌더링됩니다.
        - 해결 방법: 필터링 후의 값을 저장하기 위해 **`stateA`**를 만들지 말고, 간단한 변수를 사용하여 **`const normalA = filter(list);`**와 같이 계산하면 됩니다. 이 변수는 **`list`**가 변경될 때마다 다시 계산됩니다.
    - 사용자 이벤트 처리를 위해 효과를 사용할 필요가 없는 경우
        
        예를 들어, 사용자가 제품을 구매할 때 **`/api/buy`** POST 요청을 보내고 알림을 표시하려는 경우입니다. 구매 버튼 클릭 이벤트 핸들러에서는 사용자의 동작을 정확히 알 수 있습니다. 효과가 실행될 때는 사용자가 무엇을 했는지(예: 어떤 버튼이 클릭되었는지) 알 수 없습니다. 그래서 일반적으로 사용자 이벤트는 해당하는 이벤트 핸들러에서 처리합니다.
        
- 다음은 useEffect()를 제거할 수 있는 몇 가지 사례입니다.

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

- 상황: Zalo를 사용하면서 다른 사람에게 메시지를 보내기 위해 선택한 경우, 작성된 메시지는 재설정되어야 합니다.

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