在我日常开发中，使用react+typescript已经很成为常态化。然后经常是有any类型也不是个好办法，既low也不适合维护，所以我们学习下高级类型如何运用，为了能在开发过程中提效，代码更优雅🐶

1. Pick<Type, Keys>
```typescript
// 原理：
type Pick<T, K extends keyof T> = { [P in K]: T[P] }

example:

interface Test {
  name: string;
  age: number;
  address: string;
}

type People = Pick<Test, "name" | "age">

// People = "name" | "age"

// 含义：是在类型T中，找到K中公共的类型
```

2. Partial<Type>
```typescript
// 原理：
 type Partial<T> = {[P in keyof T]?: T[P] | undefined}
// 含义：让所定义的类型中的子类型，变为可以选择的属性
example

interface Test {
  color: string;
  size: string;
}

function doTest(font: Test, icon: Partial<Test>) {}

const test1 = {
  color: 'red',
  size: '12px'
}

const test2 = doTest(test1, {color: 'blue'})
```

3. Required<Type>
```typescript
// 原理
type Required<T> = { [P in keyof T]-: T[P] }

// 含义：把类型变为必填项

example
interface People {
  name?: string;
  age?: number
}
const people: People = {name: '张三'},
 // 这里类型检查就会报错
const person: Required<People> = { name: '李四' } 
```

4. Readonly<Type>
```typescript
// 原理
type Readonly<T> = { Readonly[P in keyof T]: T[P] }
// 顾名思义，属性是只读的，不能修改
 
interface Person {
  name: string;
}

const person:Readonly<Person> = {
name: 'xiaoming'
}

// 类型检测报错
person.name = 'lihua'
// Cannot assign to 'name' because it is a read-only property
```

5. Record<Keys, Type>
```typescript
type Record<K extends number | string | Symbol, T> = { [P in K]: T }

// 含义：对象的key只能是数字，字符串，symbol，value可以自定义

// 官方例子
interface CatInfo {
  age: number;
  breed: string;
}

type CatName = "miffy" | "boris" | "mordred";

const cat: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 10, breed: "Persian" },
  mordred: { age: 10, breed: "Persian" },
}

```

6. Omit<Type, Keys>
```typescript
type omit<T, K extends number| string| Symbol> = { [ P in Exclude<keyof T, K>]: T[P] }

// 含义：包含Type中除了keys以外的类型

type Todo {
  title: string;
  description: string;
}

type TodoPreview = Omit<Todo, "descript">

// TodoPreview = { title: string }
```

7. Exclude<UniType, ExcludMembers>
```typescript
// 原理
type Exclude<T, U> = { T extends U ? never : T }

// 含义： 排除T和U类型中的公共类型，取出不同的类型。假如T和U相同，则返回never类型

type T0 = Exclude<"a" | "b" | "c", "a">

// type T0 = "b" | "c"

type T1 = Exclude<"a" | "b" | "c", "a" | "b">

// type T1 = "c"

type T2 = Exclud<"a" | "b" | "c", "a" | "b" | "c">

// type T2 = never


```

8. Extract<Type, Union>
```typescript
type Extract<T, U> = { T extends U ? T : never }

// 含义： 找出联合类型中公共的类型

type T0 = Exclude<"a" | "b" | "c", "a">

// type T0 = "a"

```

9. NonNullable<Type>
```typescript
type NonNullable<T> = { T extends null | undefined ? never : T }

// 含义：排除类型中的null和undefined类型

type T0 = NonNullable<string | null | undefined>
// type T0 = string
```

10. Parameters<Type>
```typescript
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never 
```
