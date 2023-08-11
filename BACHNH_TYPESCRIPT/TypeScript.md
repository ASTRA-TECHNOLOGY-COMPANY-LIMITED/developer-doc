# TypeScript

## I. Giới thiệu

- TypeScript là ngôn ngữ mở rộng của JavaScript, cho phép thêm cú pháp mới.
- Nguyên nhân
  - JavaScript là một ngôn ngữ lỏng lẻo, khai báo không cần kiểu dữ liệu → Khó để biết kiểu dữ liệu của dữ liệu
  - TypeScript cho phép ta khai báo biến với kiểu dữ liệu, báo lỗi nếu kiểu dữ liệu không phù hợp.

## II. Kiểu dữ liệu nguyên thủy

- Có 3 kiểu dữ liệu nguyên thủy trong TypeScript
  1. `boolean`
  2. `number`
  3. `string`

### 1. Khai báo biến

- Có hai cách khai báo biến
  1. **Explicit:** Khai báo biến cùng với kiểu dữ liệu của biến
     - `let firstName: string = "Dylan";`
  2. **Implicit:** Khai báo biến không cần kiểu dữ liệu. TypeScript sẽ tự đoán kiểu dữ liệu của biến đó. (Ngắn, nhanh, thường sử dụng)
     - `let firstName = "Dylan";`

> Việc TypeScript tự đoán kiểu dữ liệu của biến gọi là **infer**

### 2. Lỗi khi khai báo biến

- TypeScript sẽ throw an error nếu kiểu dữ liệu không phù hợp
  - JavaScript thì không

```jsx
let firstName: string = "Dylan"; // type string
firstName = 33; // attempts to re-assign the value to a different type

let firstName = "Dylan"; // inferred to type string
firstName = 33; // attempts to re-assign the value to a different type
```

### 3. Không phải lúc nào cũng infer đúng kiểu dữ liệu

- TypeScript không phải lúc nào cũng đoán được kiểu dữ liệu của biến.
- Nếu không đoán được, biến sẽ được gán kiểu `any` (sẽ nói ở phần sau) which disables type checking.

```jsx
// Implicit any as JSON.parse doesn't know what type of data it returns so it can be "any" thing...
const json = JSON.parse("55");
// Most expect json to be an object, but it can be a string or a number like this example
console.log(typeof json);
```

## III. Kiểu dữ liệu đặc biệt

### 1. Kiểu dữ liệu `any`

- `any` cho phép ta khai báo biến thuộc kiểu dữ liệu bất kỳ bằng cách disable type checking

```jsx
let v: any = true;
v = "string"; // no error as it can be "any" type
```

> `any` có thể giúp ta tránh được các lỗi nhưng nó cũng khiến cho một số tool không thể hoạt động. Vậy nên ta cần phải tránh sử dụng nó với bất cứ giá nào.

### 2. Kiểu dữ liệu `unknown`

- `unknown` khá tương tự với `any`, nhưng an toàn hơn.
- Khi khai báo một biến `unknown`, TypeScript sẽ cố ngăn ta sử dụng nó.

  - Để có thể dùng nó , ta cần phải ****cast**** nó trước khi sử dụng (sử dụng `as`)

  ```jsx
  let w: unknown = 1;
  w = "string"; // no error
  w = {
    runANonExistentMethod: () => {
      console.log("I think therefore I am");
    }
  } as { runANonExistentMethod: () => void}
  ```

> `unknow` là kiểu dữ liệu phù hợp nhất khi ta không biết kiểu dữ liệu cần thiết của biến là gì.

### 3. Kiểu dữ liệu `never`

- `never` sẽ ngay lập tức throws an error khi nó được khai báo.

  ```tsx
  let x: never = true; // Error: Type 'boolean' is not assignable to type 'never'.
  ```

> Nó rất hiếm khi sử dụng, chỉ dùng tại cái trường hợp nâng cao.

### 4. Kiểu dữ liệu `null` và `undefined`

- Giống trong JavaScript

  ```tsx
  let y: undefined = undefined;
  let z: null = null;
  ```

## IV. Mảng

### 1. Khai báo Explicit

- Mảng có thể chỉ chứa các dữ liệu có đúng kiểu dữ liệu đã khai báo.

```tsx
const names: string[] = [];
names.push("Dylan"); // no error
// names.push(3); // Error: Argument of type 'number' is not assignable to parameter of type 'string'.
```

### 2. Khai báo Implicit

```tsx
const numbers = [1, 2, 3]; // inferred to type number[]
numbers.push(4); // no error
// comment line below out to see the successful assignment
numbers.push("2"); // Error: Argument of type 'string' is not assignable to parameter of type 'number'.
let head: number = numbers[0]; // no error
```

### 3. `readonly`

- Không cho phép thay đổi mảng, phải gán giá trị ngay khi khai báo

```tsx
const names: readonly string[] = ["Dylan"];
names.push("Jack"); // Error: Property 'push' does not exist on type 'readonly string[]'.
// try removing the readonly modifier and see if it works?
```

## V. Tuples

### 1. Typed Array

- Tuple là một Typed Array mà ta phải khai báo độ dài và kiểu dữ liệu của đối tượng trong mảng.
- Ta có thể bổ sung thêm phần tử cho Tuple bằng hàm `push()`.

```tsx
// define our tuple
let ourTuple: [number, boolean, string];
// initialize correctly
ourTuple = [5, false, 'Coding God was here'];
// We have no type safety in our tuple for indexes 3+
ourTuple.push('Something new and wrong');
console.log(ourTuple);
```

### 2. `readonly`

- Tuple nên ở trạng thái `readonly` vì những phần tử thêm vào sẽ không được khai báo kiểu dữ liệu mà TypeScript sẽ đoán nó.

```tsx
// define our readonly tuple
const ourReadonlyTuple: readonly [number, boolean, string] = [5, true, 'The Real Coding God'];
// throws error as it is readonly.
ourReadonlyTuple.push('Coding God took a day off');
```

> `useState()` trong React trả về 1 tuple `[state, setState]`.

### 3. Đặt tên Tuple

```tsx
const graph: [x: number, y: number] = [55.2, 41.3];
```

### 4. Destructuring Tuple

```tsx
const graph: [number, number] = [55.2, 41.3];
const [x, y] = graph;
```

## VI. Object

### 1. Khai báo Explicit

- Trong TypeScript, ta phải khai báo Object một cách cụ thể .

```tsx
const car: { type: string, model: string, year: number } = {
  type: "Toyota",
  model: "Corolla",
  year: 2009
};
```

### 2. Khai báo Implicit

- TypeScript có thể đoán được kiểu dữ liệu của các Object trong TypeScript

```tsx
const car = {
  type: "Toyota",
};
car.type = "Ford"; // no error
car.type = 2; // Error: Type 'number' is not assignable to type 'string'.
```

### 3. Optional Properties

```tsx
const car: { type: string, mileage?: number } = { // no error
  type: "Toyota"
};
car.mileage = 2000;
```

### 4. Index Signatures

- Ta có thể định nghĩa hàng loạt Properties mà không cần đặt tên cho từng cái.

```tsx
const nameAgeMap: { [index: string]: number } = {};
nameAgeMap.Jack = 25; // no error
nameAgeMap.Mark = "Fifty"; // Error: Type 'string' is not assignable to type 'number'.
```

## VII. Aliases và Interfaces

### 1. Type Aliases

- Type Aliases cho phép ta đặt tên cho một kiểu dữ liệu nào đó.

```tsx
type CarYear = number
type CarType = string
type CarModel = string
type Car = {
  year: CarYear,
  type: CarType,
  model: CarModel
}

const carYear: CarYear = 2001
const carType: CarType = "Toyota"
const carModel: CarModel = "Corolla"
const car: Car = {
  year: carYear,
  type: carType,
  model: carModel
};
```

### 2. Interfaces

- Interfaces tương tự với Aliases, nhưng ******chỉ áp dụng với `object`.**

```tsx
interface Rectangle {
  height: number,
  width: number
}

const rectangle: Rectangle = {
  height: 20,
  width: 10
};
```

- **Kế thừa**: Interface có thể kế thừa lẫn nhau

  ```tsx
  interface Rectangle {
    height: number,
    width: number
  }

  interface ColoredRectangle extends Rectangle {
    color: string
  }

  const coloredRectangle: ColoredRectangle = {
    height: 20,
    width: 10,
    color: "red"
  };
  ```

## VIII. Union Type

### 1. Union `|`

- Union là việc ta thông báo rằng tham số có thể thuộc một trong vài kiểu dữ liệu.

```tsx
function printStatusCode(code: string | number) {
  console.log(`My status code is ${code}.`)
}
printStatusCode(404);
printStatusCode('404');
```

### 2. Lỗi khi sử dụng Union

```tsx
function printStatusCode(code: string | number) {

	// error: Property 'toUpperCase' does not exist ontype 'string | number'.
  console.log(`My status code is ${code.toUpperCase()}.`) 
  
	Property 'toUpperCase' does not exist on type 'number'
}
```

## IX. Function

### 1. Return type

- Ta có thể khai báo explicit (khai báo cả dữ liệu trả về) cho Function.
- Nếu không khai báo return type, TS sẽ **infer** dựa vào biến mà biểu thức trả về.

```tsx
// the `: number` here specifies that this function returns a number
function getTime(): number {
  return new Date().getTime();
}
```

- Nếu không trả về dữ liệu nào thì để function type là `void`.

### 2. Tham số

- Tương tự với khai báo biến.

```tsx
function multiply(a: number, b: number) {
  return a * b;
}
```

> Nếu không khai báo kiểu cho biến, TS sẽ mặc định cho biến đó kiểu `any` (việc này là không tốt). Ta có thể để TS infer bằng cách:
>
> - Gán giá trị mặc định cho tham số.
> - Information is available as shown in the Type Aliases section below.

### 3. Optional Parameters

- TypeScript sẽ mặc định để tất cả tham số là bắt buộc. Để cho nó trở thành optional, ta sử dụng `?`.

```tsx
// the `?` operator here marks parameter `c` as optional
function add(a: number, b: number, c?: number) {
  return a + b + (c || 0);
}
```

### 4. Default Parameters

```tsx
function pow(value: number, exponent: number = 10) {
  return value ** exponent;
}
```

### 5. Type Alias

```tsx
type Negate = (value: number) => number;

// in this function, the parameter `value` automatically gets assigned the type `number` from the type `Negate`
const negateFunction: Negate = (value) => value * -1;
```

## X. Casting

### 1. Casting?

- Casting là việc ta ép kiểu dữ liệu của một biến.

### 2. `as`

```tsx
let x: unknown = 'hello';
console.log((x as string).length);
```

> Casting không làm thay đổi kiểu dữ liệu của biến mà nó chỉ cố gắng hiểu biến dưới dạng kiểu dữ liệu khác..
>
> ```tsx
> let x: unknown = 4;
> console.log((x as string).length); // prints undefined since numbers don't have a length
> ```

### 3. `<>`

```tsx
let x: unknown = 'hello';
console.log((<string>x).length);
```

> Không thể sử dụng `<>` trong file `.tsx`.

## XI. Classes

- TypeScript cho phép ta thêm kiểu dữ liệu và mức độ truy cập vào các thành phần của JS Classes.

### 1. Kiểu dữ liệu

```tsx
class Person {
  name: string;
}

const person = new Person();
person.name = "Jane";
```

### 2. Mức độ truy cập

> Có 3 mức độ truy cập
>
> - `public` - (default) allows access to the class member from anywhere
> - `private` - only allows access to the class member from within the class
> - `protected` - allows access to the class member from itself and any classes that inherit it, which is covered in the inheritance section below

```tsx
class Person {
  private name: string;

  public constructor(name: string) {
    this.name = name;
  }

  public getName(): string {
    return this.name;
  }
}

const person = new Person("Jane");
console.log(person.getName()); // person.name isn't accessible from outside the class since it's private
```

<aside>
💡 `this` sẽ chỉ tới một đối tượng (instance) của class.

</aside>

### 3. Thêm thuộc tính bằng tham số của Construcotr

```tsx
class Person {
  // name is a private member variable
  public constructor(private name: string) {}

  public getName(): string {
    return this.name;
  }
}

const person = new Person("Jane");
console.log(person.getName());
```

### 4. Readonly

- Giúp cho các thành phần của Class không bị thay đổi.

```tsx
class Person {
  private readonly name: string;

  public constructor(name: string) {
    // name cannot be changed after this initial definition, which has to be either at it's declaration or in the constructor.
    this.name = name;
  }

  public getName(): string {
    return this.name;
  }
}

const person = new Person("Jane");
console.log(person.getName());
```

### 5. Implements

- Ta có thể tạo ra một Class kế thừa một Interface khác.

```tsx
interface Shape {
  getArea: () => number;
}

class Rectangle implements Shape {
  public constructor(protected readonly width: number, protected readonly height: number) {}

  public getArea(): number {
    return this.width * this.height;
  }
}
```

> Một Class có thể implements nhiều interfaces bằng cú pháp `class Rectangle implements Shape, Colored {`

### 6. Extends

- Class có thể kế thừa (extends) **duy nhất một** Class khác.

### 7. Override

- Khi một Class kế thừa Class khác, nó có thể Override thành phần của Class cha bằng cách đặt tên thành phần mới giống với thành phần kia.
- Newer versions of TypeScript allow explicitly marking this with the `override` keyword.
  - Thường chỉ sử dụng để tránh `override` method không tồn tại.

```tsx
interface Shape {
  getArea: () => number;
}

class Rectangle implements Shape {
  // using protected for these members allows access from classes that extend from this class, such as Square
  public constructor(protected readonly width: number, protected readonly height: number) {}

  public getArea(): number {
    return this.width * this.height;
  }

  public toString(): string {
    return `Rectangle[width=${this.width}, height=${this.height}]`;
  }
}

class Square extends Rectangle {
  public constructor(width: number) {
    super(width, width);
  }

  // this toString replaces the toString from Rectangle
  public override toString(): string {
    return `Square[width=${this.width}]`;
  }
}
```

### 8. Abstract Classes

- Class cũng có thể sử dụng để làm interface, bắt các class con phải implement một số method đã quy định từ trước.
- Sử dụng cú pháp `abstract`.

```tsx
abstract class Polygon {
  public abstract getArea(): number;

  public toString(): string {
    return `Polygon[area=${this.getArea()}]`;
  }
}

class Rectangle extends Polygon {
  public constructor(protected readonly width: number, protected readonly height: number) {
    super();
  }

  public getArea(): number {
    return this.width * this.height;
  }
}
```

> Các đối tượng của Abstract Class thường không thể khởi tạo trực tiếp.

- Wrong: `const person = new Parent();`
- True: `const person: Parent = new Child();`

## XII. Basic Generics

- Basic Generics giúp ta sử dụng kiểu dữ liệu như là một biến số.

### 1. Function

```tsx
function createPair<S, T>(v1: S, v2: T): [S, T] {
  return [v1, v2];
}
console.log(createPair<string, number>('hello', 42)); // ['hello', 42]
```

### 2. Classes

```tsx
class NamedValue<T> {
  private _value: T | undefined;

  constructor(private name: string) {}

  public setValue(value: T) {
    this._value = value;
  }

  public getValue(): T | undefined {
    return this._value;
  }

  public toString(): string {
    return `${this.name}: ${this._value}`;
  }
}

let value = new NamedValue<number>('myNumber');
value.setValue(10);
console.log(value.toString()); // myNumber: 10
```

### 3. Type Aliases

```tsx
type Wrapped<T> = { value: T };

const wrappedValue: Wrapped<number> = { value: 10 };
```

## XIII. Utility Type - Các tiện ích

### 1. Partial

- Khiến tất cả properties trở thành optional.

```tsx
interface Point {
  x: number;
  y: number;
}

let pointPart: Partial<Point> = {}; // `Partial` allows x and y to be optional
pointPart.x = 10;
```

### 2. Required

- Khiến tất cả properties thành thành required.

```tsx
interface Car {
  make: string;
  model: string;
  mileage?: number;
}

let myCar: Required<Car> = {
  make: 'Ford',
  model: 'Focus',
  mileage: 12000 // `Required` forces mileage to be defined
};
```

### 3. Record

- Định nghĩa một Object với mỗi cặp key, value sẽ có kiểu dữ liệu cụ thể

```tsx
const nameAgeMap: Record<string, number> = {
  'Alice': 21,
  'Bob': 25
};
```

> `Record<string, number>` is equivalent to `{ [key: string]: number }`

### 4. Omit

- Ngăn không cho ta khởi tạo thuộc tính có key cụ thể.

```tsx
interface Person {
  name: string;
  age: number;
  location?: string;
}

const bob: Omit<Person, 'age' | 'location'> = {
  name: 'Bob'
  // `Omit` has removed age and location from the type and they can't be defined here
};
```

### 5. Pick

- Chỉ cho ta khởi tạo thuộc tính có key cụ thể

```tsx
interface Person {
  name: string;
  age: number;
  location?: string;
}

const bob: Pick<Person, 'name'> = {
  name: 'Bob'
  // `Pick` has only kept name, so age and location were removed from the type and they can't be defined here
};
```

## XIV. Keyof

- `keyof` là cú pháp để lấy ra các kiểu dữ liệu của các thuộc tính trong một Object.

```tsx
interface Person {
  name: string;
  age: number;
}
console.log(keyof Person) // string | number
```
