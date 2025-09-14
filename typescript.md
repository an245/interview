# TypeScript 核心概念与面试题

## 1. 基础类型系统

### 核心概念
- **静态类型检查**：编译时类型检查
- **类型注解**：显式声明变量类型
- **类型推断**：自动推断变量类型
- **基础类型**：string、number、boolean、array、object等
- **联合类型**：使用 | 操作符
- **交叉类型**：使用 & 操作符

### 常问面试题

#### Q1: TypeScript和JavaScript的主要区别？
**答案：**
```typescript
// JavaScript - 动态类型
let name = "John";
name = 123; // 运行时才知道错误

// TypeScript - 静态类型
let name: string = "John";
name = 123; // 编译时就会报错

// 类型推断
let age = 25; // TypeScript自动推断为number类型
let isActive = true; // TypeScript自动推断为boolean类型

// 联合类型
let id: string | number;
id = "123";
id = 456;

// 交叉类型
interface Person {
  name: string;
}

interface Employee {
  id: number;
}

type PersonEmployee = Person & Employee;
const person: PersonEmployee = {
  name: "John",
  id: 123
};
```

#### Q2: 如何定义数组和对象类型？
**答案：**
```typescript
// 数组类型定义
let numbers: number[] = [1, 2, 3];
let names: Array<string> = ["John", "Jane"];

// 元组类型
let person: [string, number] = ["John", 25];

// 对象类型定义
interface User {
  name: string;
  age: number;
  email?: string; // 可选属性
  readonly id: number; // 只读属性
}

// 索引签名
interface Dictionary {
  [key: string]: any;
}

// 函数类型
type AddFunction = (a: number, b: number) => number;
const add: AddFunction = (a, b) => a + b;
```

## 2. 接口与类型别名

### 核心概念
- **接口**：定义对象结构，可扩展
- **类型别名**：为类型创建别名，更灵活
- **接口继承**：extends关键字
- **类型合并**：同名接口自动合并
- **泛型接口**：带类型参数的接口

### 常问面试题

#### Q3: interface和type的区别？
**答案：**
```typescript
// interface - 可扩展，主要用于对象结构
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

// 接口合并
interface User {
  name: string;
}

interface User {
  age: number;
}
// 最终User接口包含name和age

// type - 更灵活，可用于任何类型
type Status = 'pending' | 'approved' | 'rejected';

type UserType = {
  name: string;
  age: number;
};

// 条件类型
type NonNullable<T> = T extends null | undefined ? never : T;

// 主要区别：
// 1. interface可以合并，type不能
// 2. type可以用于联合类型、条件类型等
// 3. interface主要用于对象结构，type更通用
```

#### Q4: 如何实现接口继承和类型扩展？
**答案：**
```typescript
// 接口继承
interface BaseEntity {
  id: number;
  createdAt: Date;
}

interface User extends BaseEntity {
  name: string;
  email: string;
}

// 类型扩展
type BaseEntityType = {
  id: number;
  createdAt: Date;
};

type UserType = BaseEntityType & {
  name: string;
  email: string;
};

// 泛型接口
interface Repository<T> {
  findById(id: number): T | null;
  save(entity: T): T;
  delete(id: number): boolean;
}

// 使用泛型接口
interface UserRepository extends Repository<User> {
  findByEmail(email: string): User | null;
}
```

## 3. 泛型

### 核心概念
- **泛型函数**：带类型参数的函数
- **泛型接口**：带类型参数的接口
- **泛型类**：带类型参数的类
- **泛型约束**：限制泛型类型范围
- **条件类型**：基于条件选择类型

### 常问面试题

#### Q5: 什么是泛型？如何使用？
**答案：**
```typescript
// 泛型函数
function identity<T>(arg: T): T {
  return arg;
}

const result1 = identity<string>("hello");
const result2 = identity<number>(42);
const result3 = identity("hello"); // 类型推断

// 泛型接口
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

// 泛型类
class Container<T> {
  private items: T[] = [];
  
  add(item: T): void {
    this.items.push(item);
  }
  
  get(index: number): T | undefined {
    return this.items[index];
  }
}

// 泛型约束
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}

logLength("hello"); // 字符串有length属性
logLength([1, 2, 3]); // 数组有length属性
```

#### Q6: 实现一个通用的API请求函数
**答案：**
```typescript
// 定义API响应类型
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

// 定义请求配置
interface RequestConfig {
  method: 'GET' | 'POST' | 'PUT' | 'DELETE';
  headers?: Record<string, string>;
  body?: any;
}

// 泛型API请求函数
async function apiRequest<T>(
  url: string, 
  config: RequestConfig = { method: 'GET' }
): Promise<ApiResponse<T>> {
  try {
    const response = await fetch(url, {
      method: config.method,
      headers: {
        'Content-Type': 'application/json',
        ...config.headers
      },
      body: config.body ? JSON.stringify(config.body) : undefined
    });
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return {
      data,
      status: response.status,
      message: 'Success'
    };
  } catch (error) {
    throw new Error(`API request failed: ${error.message}`);
  }
}

// 使用示例
interface User {
  id: number;
  name: string;
  email: string;
}

// 获取用户列表
const users = await apiRequest<User[]>('/api/users');

// 创建用户
const newUser = await apiRequest<User>('/api/users', {
  method: 'POST',
  body: { name: 'John', email: 'john@example.com' }
});
```

## 4. 高级类型

### 核心概念
- **映射类型**：基于现有类型创建新类型
- **条件类型**：根据条件选择类型
- **模板字面量类型**：字符串模板类型
- **索引访问类型**：通过索引访问类型
- **keyof操作符**：获取对象键的类型

### 常问面试题

#### Q7: 实现Partial、Required、Pick等工具类型
**答案：**
```typescript
// Partial - 所有属性变为可选
type MyPartial<T> = {
  [P in keyof T]?: T[P];
};

// Required - 所有属性变为必需
type MyRequired<T> = {
  [P in keyof T]-?: T[P];
};

// Pick - 选择特定属性
type MyPick<T, K extends keyof T> = {
  [P in K]: T[P];
};

// Omit - 排除特定属性
type MyOmit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;

// Record - 创建键值对类型
type MyRecord<K extends keyof any, T> = {
  [P in K]: T;
};

// 使用示例
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

type PartialUser = MyPartial<User>; // 所有属性可选
type RequiredUser = MyRequired<User>; // 所有属性必需
type UserName = MyPick<User, 'name' | 'email'>; // 只有name和email
type UserWithoutId = MyOmit<User, 'id'>; // 排除id属性
type UserMap = MyRecord<string, User>; // { [key: string]: User }
```

#### Q8: 条件类型和infer关键字
**答案：**
```typescript
// 条件类型基础
type IsString<T> = T extends string ? true : false;

type Test1 = IsString<string>; // true
type Test2 = IsString<number>; // false

// infer关键字 - 类型推断
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type FuncReturnType = ReturnType<() => string>; // string
type AsyncReturnType = ReturnType<() => Promise<number>>; // Promise<number>

// 提取数组元素类型
type ArrayElement<T> = T extends (infer U)[] ? U : never;

type StringArray = string[];
type ElementType = ArrayElement<StringArray>; // string

// 提取Promise类型
type Awaited<T> = T extends Promise<infer U> ? U : T;

type PromiseString = Promise<string>;
type ResolvedType = Awaited<PromiseString>; // string

// 递归条件类型
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

interface NestedObject {
  a: {
    b: {
      c: string;
    };
  };
}

type ReadonlyNested = DeepReadonly<NestedObject>;
```

## 5. 模块与命名空间

### 核心概念
- **模块系统**：ES6模块语法
- **命名空间**：namespace关键字
- **模块声明**：declare module
- **环境声明**：declare关键字
- **类型声明文件**：.d.ts文件

### 常问面试题

#### Q9: 如何为第三方库添加类型声明？
**答案：**
```typescript
// 为没有类型声明的库创建声明文件
// types/custom-lib.d.ts
declare module 'custom-lib' {
  interface Config {
    apiKey: string;
    timeout?: number;
  }
  
  class CustomLib {
    constructor(config: Config);
    request(url: string): Promise<any>;
  }
  
  export = CustomLib;
}

// 扩展现有模块
declare module 'express' {
  interface Request {
    user?: {
      id: number;
      name: string;
    };
  }
}

// 全局声明
declare global {
  interface Window {
    myGlobalVar: string;
  }
}

// 命名空间
namespace MyNamespace {
  export interface User {
    id: number;
    name: string;
  }
  
  export class UserService {
    getUser(id: number): User {
      return { id, name: 'John' };
    }
  }
}

// 使用命名空间
const user = MyNamespace.UserService.prototype.getUser(1);
```

## 6. 装饰器

### 核心概念
- **类装饰器**：修饰类定义
- **方法装饰器**：修饰类方法
- **属性装饰器**：修饰类属性
- **参数装饰器**：修饰函数参数
- **装饰器工厂**：返回装饰器的函数

### 常问面试题

#### Q10: 实现一个简单的装饰器
**答案：**
```typescript
// 启用装饰器支持
// tsconfig.json: "experimentalDecorators": true

// 方法装饰器 - 日志记录
function log(target: any, propertyName: string, descriptor: PropertyDescriptor) {
  const method = descriptor.value;
  
  descriptor.value = function(...args: any[]) {
    console.log(`调用方法: ${propertyName}`);
    console.log(`参数:`, args);
    const result = method.apply(this, args);
    console.log(`返回值:`, result);
    return result;
  };
}

// 属性装饰器 - 验证
function validate(target: any, propertyName: string) {
  let value = target[propertyName];
  
  const getter = () => value;
  const setter = (newValue: any) => {
    if (typeof newValue !== 'string') {
      throw new Error(`${propertyName} 必须是字符串`);
    }
    value = newValue;
  };
  
  Object.defineProperty(target, propertyName, {
    get: getter,
    set: setter,
    enumerable: true,
    configurable: true
  });
}

// 类装饰器 - 单例模式
function singleton<T extends { new(...args: any[]): {} }>(constructor: T) {
  let instance: T;
  
  return class extends constructor {
    constructor(...args: any[]) {
      if (instance) {
        return instance;
      }
      super(...args);
      instance = this as any;
    }
  };
}

// 使用装饰器
@singleton
class UserService {
  @validate
  private name: string = '';
  
  @log
  getUser(id: number): string {
    return `User ${id}`;
  }
  
  setName(name: string): void {
    this.name = name;
  }
}
```

## 7. 类型守卫与断言

### 核心概念
- **类型守卫**：运行时类型检查
- **类型断言**：告诉编译器类型信息
- **用户定义类型守卫**：自定义类型检查函数
- **in操作符**：检查属性是否存在
- **typeof操作符**：检查基本类型

### 常问面试题

#### Q11: 类型守卫和类型断言的区别？
**答案：**
```typescript
// 类型守卫 - 运行时检查
function isString(value: any): value is string {
  return typeof value === 'string';
}

function isNumber(value: any): value is number {
  return typeof value === 'number';
}

function processValue(value: string | number) {
  if (isString(value)) {
    // TypeScript知道这里value是string类型
    console.log(value.toUpperCase());
  } else if (isNumber(value)) {
    // TypeScript知道这里value是number类型
    console.log(value.toFixed(2));
  }
}

// 类型断言 - 编译时告诉编译器类型
function getValue(): unknown {
  return "hello";
}

const value = getValue();
// 类型断言方式1: as语法
const str1 = value as string;
// 类型断言方式2: 尖括号语法
const str2 = <string>value;

// 非空断言
function getElement(): HTMLElement | null {
  return document.getElementById('myElement');
}

const element = getElement()!; // 告诉编译器这里不会是null

// 用户定义类型守卫
interface Bird {
  fly(): void;
  layEggs(): void;
}

interface Fish {
  swim(): void;
  layEggs(): void;
}

function isBird(pet: Bird | Fish): pet is Bird {
  return (pet as Bird).fly !== undefined;
}

function move(pet: Bird | Fish) {
  if (isBird(pet)) {
    pet.fly(); // TypeScript知道pet是Bird类型
  } else {
    pet.swim(); // TypeScript知道pet是Fish类型
  }
}
```

## 8. 工具类型详解

### 核心概念
- **内置工具类型**：TypeScript提供的工具类型
- **自定义工具类型**：根据需求创建的工具类型
- **类型操作**：对类型进行变换和操作
- **类型组合**：将多个类型组合成新类型

### 常问面试题

#### Q12: 实现常用的工具类型
**答案：**
```typescript
// 提取函数参数类型
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;

// 提取构造函数参数类型
type ConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer P) => any ? P : never;

// 提取实例类型
type InstanceType<T extends new (...args: any) => any> = T extends new (...args: any) => infer R ? R : any;

// 提取Promise类型
type Awaited<T> = T extends Promise<infer U> ? U : T;

// 深度可选
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

// 深度只读
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

// 获取对象值的类型
type ValueOf<T> = T[keyof T];

// 获取数组元素类型
type ElementType<T> = T extends (infer U)[] ? U : never;

// 使用示例
interface User {
  id: number;
  name: string;
  profile: {
    age: number;
    email: string;
  };
}

type UserPartial = DeepPartial<User>;
type UserReadonly = DeepReadonly<User>;
type UserValues = ValueOf<User>; // number | string | { age: number; email: string; }
```

## 9. 错误处理与类型安全

### 核心概念
- **类型安全**：编译时类型检查
- **错误处理**：类型化的错误处理
- **Result类型**：函数式错误处理
- **Option类型**：可选值处理
- **异常类型**：类型化的异常

### 常问面试题

#### Q13: 实现类型安全的错误处理
**答案：**
```typescript
// Result类型 - 函数式错误处理
type Result<T, E = Error> = Success<T> | Failure<E>;

interface Success<T> {
  success: true;
  data: T;
}

interface Failure<E> {
  success: false;
  error: E;
}

// Result工具函数
function success<T>(data: T): Success<T> {
  return { success: true, data };
}

function failure<E>(error: E): Failure<E> {
  return { success: false, error };
}

// 使用Result类型
async function fetchUser(id: number): Promise<Result<User, string>> {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      return failure(`HTTP error: ${response.status}`);
    }
    const user = await response.json();
    return success(user);
  } catch (error) {
    return failure(`Network error: ${error.message}`);
  }
}

// 处理Result
async function handleUser() {
  const result = await fetchUser(1);
  
  if (result.success) {
    console.log('用户:', result.data);
  } else {
    console.error('错误:', result.error);
  }
}

// Option类型 - 可选值处理
type Option<T> = Some<T> | None;

interface Some<T> {
  type: 'some';
  value: T;
}

interface None {
  type: 'none';
}

function some<T>(value: T): Some<T> {
  return { type: 'some', value };
}

function none(): None {
  return { type: 'none' };
}

// 使用Option类型
function findUser(users: User[], id: number): Option<User> {
  const user = users.find(u => u.id === id);
  return user ? some(user) : none();
}
```

## 10. 性能优化与最佳实践

### 核心概念
- **类型性能**：避免复杂类型计算
- **模块分割**：合理组织类型定义
- **类型缓存**：缓存复杂类型
- **编译优化**：TypeScript编译选项
- **类型检查优化**：减少类型检查时间

### 常问面试题

#### Q14: TypeScript性能优化技巧
**答案：**
```typescript
// 1. 使用类型别名缓存复杂类型
type ComplexType = {
  // 复杂的类型定义
  [K in string]: {
    [P in string]: {
      [Q in string]: string;
    };
  };
};

// 2. 避免深层嵌套的类型
// 不好的做法
type BadType = {
  level1: {
    level2: {
      level3: {
        level4: {
          level5: string;
        };
      };
    };
  };
};

// 好的做法
interface Level5 {
  value: string;
}

interface Level4 {
  level5: Level5;
}

interface Level3 {
  level4: Level4;
}

interface Level2 {
  level3: Level3;
}

interface GoodType {
  level1: Level2;
}

// 3. 使用条件类型优化
type NonNullable<T> = T extends null | undefined ? never : T;

// 4. 合理使用泛型约束
interface Repository<T extends { id: number }> {
  findById(id: number): T | null;
  save(entity: T): T;
}

// 5. 使用映射类型优化
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// 6. 避免any类型
// 不好的做法
function processData(data: any): any {
  return data.someProperty;
}

// 好的做法
function processData<T extends { someProperty: unknown }>(data: T): T['someProperty'] {
  return data.someProperty;
}
```

## 总结

TypeScript面试重点：
1. **基础类型系统**：类型注解、类型推断、联合类型、交叉类型
2. **接口与类型别名**：interface vs type、继承、合并
3. **泛型**：泛型函数、接口、类、约束、条件类型
4. **高级类型**：映射类型、条件类型、模板字面量类型
5. **模块与命名空间**：ES6模块、类型声明、环境声明
6. **装饰器**：类装饰器、方法装饰器、属性装饰器
7. **类型守卫与断言**：运行时类型检查、类型断言
8. **工具类型**：内置工具类型、自定义工具类型
9. **错误处理**：Result类型、Option类型、类型安全
10. **性能优化**：类型性能、编译优化、最佳实践
