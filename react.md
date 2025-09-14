# React 核心概念与面试题

## 1. React基础概念

### 核心概念
- **组件化**：可复用的UI单元
- **虚拟DOM**：JavaScript对象表示DOM
- **JSX**：JavaScript XML语法扩展
- **单向数据流**：数据从父组件流向子组件
- **声明式编程**：描述UI应该是什么样子

### 常问面试题

#### Q1: React是什么？有什么特点？
**答案：**
```jsx
// React是一个用于构建用户界面的JavaScript库
// 主要特点：

// 1. 组件化
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// 2. 虚拟DOM
const element = <h1>Hello, world</h1>;

// 3. JSX语法
function App() {
  const name = "React";
  return (
    <div>
      <h1>Hello, {name}</h1>
      <Welcome name="World" />
    </div>
  );
}

// 4. 单向数据流
function Parent() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <Child count={count} onIncrement={() => setCount(count + 1)} />
    </div>
  );
}

function Child({ count, onIncrement }) {
  return (
    <button onClick={onIncrement}>
      Count: {count}
    </button>
  );
}
```

#### Q2: 虚拟DOM是什么？为什么使用虚拟DOM？
**答案：**
```jsx
// 虚拟DOM是真实DOM的JavaScript表示
const virtualDOM = {
  type: 'div',
  props: {
    className: 'container',
    children: [
      {
        type: 'h1',
        props: {
          children: 'Hello World'
        }
      }
    ]
  }
};

// 虚拟DOM的优势：
// 1. 性能优化 - 批量更新，减少DOM操作
// 2. 跨平台 - 可以渲染到不同平台
// 3. 开发体验 - 声明式编程

// React的diff算法
function List({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

// key属性的作用：帮助React识别哪些元素发生了变化
```

## 2. 组件与Props

### 核心概念
- **函数组件**：使用函数定义的组件
- **类组件**：使用class定义的组件
- **Props**：组件间传递的数据
- **PropTypes**：类型检查
- **默认Props**：默认属性值

### 常问面试题

#### Q3: 函数组件和类组件的区别？
**答案：**
```jsx
// 函数组件 - 现代React推荐
function FunctionComponent({ name, age }) {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);
  
  return (
    <div>
      <h1>Hello, {name}</h1>
      <p>Age: {age}</p>
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
    </div>
  );
}

// 类组件 - 传统方式
class ClassComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  componentDidMount() {
    document.title = `Count: ${this.state.count}`;
  }
  
  componentDidUpdate() {
    document.title = `Count: ${this.state.count}`;
  }
  
  render() {
    return (
      <div>
        <h1>Hello, {this.props.name}</h1>
        <p>Age: {this.props.age}</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Count: {this.state.count}
        </button>
      </div>
    );
  }
}

// 主要区别：
// 1. 语法：函数组件更简洁
// 2. 状态管理：函数组件使用useState，类组件使用this.state
// 3. 生命周期：函数组件使用useEffect，类组件使用生命周期方法
// 4. this绑定：函数组件没有this，类组件需要处理this绑定
```

#### Q4: 如何实现组件的默认Props和类型检查？
**答案：**
```jsx
// 使用PropTypes进行类型检查
import PropTypes from 'prop-types';

function UserCard({ name, age, email, isActive }) {
  return (
    <div className={`user-card ${isActive ? 'active' : 'inactive'}`}>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}

// 定义PropTypes
UserCard.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
  email: PropTypes.string,
  isActive: PropTypes.bool
};

// 定义默认Props
UserCard.defaultProps = {
  age: 0,
  email: 'No email provided',
  isActive: true
};

// 使用TypeScript的类型检查
interface UserCardProps {
  name: string;
  age?: number;
  email?: string;
  isActive?: boolean;
}

function UserCardTS({ 
  name, 
  age = 0, 
  email = 'No email provided', 
  isActive = true 
}: UserCardProps) {
  return (
    <div className={`user-card ${isActive ? 'active' : 'inactive'}`}>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}
```

## 3. State与生命周期

### 核心概念
- **State**：组件内部状态
- **useState**：函数组件状态管理
- **useEffect**：副作用处理
- **生命周期方法**：类组件的生命周期
- **状态更新**：异步状态更新

### 常问面试题

#### Q5: useState和useEffect的使用？
**答案：**
```jsx
import { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  // useEffect相当于componentDidMount + componentDidUpdate
  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]); // 依赖数组，只有count变化时才执行
  
  // 相当于componentDidMount
  useEffect(() => {
    console.log('组件挂载');
    
    // 清理函数，相当于componentWillUnmount
    return () => {
      console.log('组件卸载');
    };
  }, []); // 空依赖数组，只在挂载时执行一次
  
  // 多个useEffect
  useEffect(() => {
    // 处理name变化
    console.log('Name changed:', name);
  }, [name]);
  
  return (
    <div>
      <input 
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter name"
      />
      <p>Hello, {name}</p>
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
    </div>
  );
}

// 函数式更新
function CounterWithFunctionalUpdate() {
  const [count, setCount] = useState(0);
  
  const increment = () => {
    setCount(prevCount => prevCount + 1);
  };
  
  const incrementBy = (amount) => {
    setCount(prevCount => prevCount + amount);
  };
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+1</button>
      <button onClick={() => incrementBy(5)}>+5</button>
    </div>
  );
}
```

#### Q6: 类组件的生命周期方法？
**答案：**
```jsx
class LifecycleDemo extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    console.log('1. Constructor');
  }
  
  static getDerivedStateFromProps(props, state) {
    console.log('2. getDerivedStateFromProps');
    return null;
  }
  
  componentDidMount() {
    console.log('4. componentDidMount');
    // 在这里进行API调用、设置定时器等
  }
  
  shouldComponentUpdate(nextProps, nextState) {
    console.log('5. shouldComponentUpdate');
    return nextState.count !== this.state.count;
  }
  
  getSnapshotBeforeUpdate(prevProps, prevState) {
    console.log('6. getSnapshotBeforeUpdate');
    return null;
  }
  
  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log('7. componentDidUpdate');
  }
  
  componentWillUnmount() {
    console.log('8. componentWillUnmount');
    // 清理定时器、取消网络请求等
  }
  
  render() {
    console.log('3. Render');
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}

// 生命周期执行顺序：
// 1. Constructor
// 2. getDerivedStateFromProps
// 3. Render
// 4. componentDidMount
// 5. shouldComponentUpdate (更新时)
// 6. getSnapshotBeforeUpdate (更新时)
// 7. componentDidUpdate (更新时)
// 8. componentWillUnmount (卸载时)
```

## 4. Hooks详解

### 核心概念
- **useState**：状态管理
- **useEffect**：副作用处理
- **useContext**：上下文消费
- **useReducer**：复杂状态管理
- **useMemo**：性能优化
- **useCallback**：函数缓存
- **自定义Hooks**：逻辑复用

### 常问面试题

#### Q7: 自定义Hooks的实现？
**答案：**
```jsx
// 自定义Hook：数据获取
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const result = await response.json();
        setData(result);
        setError(null);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    
    fetchData();
  }, [url]);
  
  return { data, loading, error };
}

// 自定义Hook：本地存储
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });
  
  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };
  
  return [storedValue, setValue];
}

// 自定义Hook：防抖
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);
  
  return debouncedValue;
}

// 使用自定义Hooks
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  const [searchTerm, setSearchTerm] = useState('');
  const debouncedSearchTerm = useDebounce(searchTerm, 500);
  
  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <div className={`profile ${theme}`}>
      <h1>{user.name}</h1>
      <input 
        value={searchTerm}
        onChange={(e) => setSearchTerm(e.target.value)}
        placeholder="Search..."
      />
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </div>
  );
}
```

#### Q8: useMemo和useCallback的区别？
**答案：**
```jsx
import { useState, useMemo, useCallback, memo } from 'react';

// 使用useMemo缓存计算结果
function ExpensiveComponent({ items, filter }) {
  const filteredItems = useMemo(() => {
    console.log('Filtering items...');
    return items.filter(item => item.category === filter);
  }, [items, filter]); // 只有items或filter变化时才重新计算
  
  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

// 使用useCallback缓存函数
function ParentComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  // 缓存函数，避免子组件不必要的重新渲染
  const handleClick = useCallback(() => {
    console.log('Button clicked');
  }, []); // 空依赖数组，函数不会改变
  
  const handleNameChange = useCallback((newName) => {
    setName(newName);
  }, []); // 空依赖数组，函数不会改变
  
  return (
    <div>
      <input 
        value={name}
        onChange={(e) => handleNameChange(e.target.value)}
      />
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
      <ChildComponent onClick={handleClick} />
    </div>
  );
}

// 使用memo优化子组件
const ChildComponent = memo(({ onClick }) => {
  console.log('ChildComponent rendered');
  return <button onClick={onClick}>Click me</button>;
});

// 复杂计算示例
function DataProcessor({ data, options }) {
  const processedData = useMemo(() => {
    console.log('Processing data...');
    return data.map(item => ({
      ...item,
      processed: item.value * options.multiplier,
      formatted: new Date(item.date).toLocaleDateString()
    }));
  }, [data, options]);
  
  const sortedData = useMemo(() => {
    console.log('Sorting data...');
    return processedData.sort((a, b) => a.processed - b.processed);
  }, [processedData]);
  
  return (
    <div>
      {sortedData.map(item => (
        <div key={item.id}>
          {item.name}: {item.processed} ({item.formatted})
        </div>
      ))}
    </div>
  );
}
```

## 5. 状态管理

### 核心概念
- **useReducer**：复杂状态管理
- **Context API**：跨组件状态共享
- **Redux**：全局状态管理
- **状态提升**：将状态提升到共同父组件
- **状态模式**：状态管理的最佳实践

### 常问面试题

#### Q9: useReducer的使用场景？
**答案：**
```jsx
import { useReducer } from 'react';

// 定义reducer函数
function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, {
          id: Date.now(),
          text: action.payload,
          completed: false
        }]
      };
    case 'TOGGLE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        )
      };
    case 'DELETE_TODO':
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload)
      };
    case 'SET_FILTER':
      return {
        ...state,
        filter: action.payload
      };
    default:
      return state;
  }
}

// 初始状态
const initialState = {
  todos: [],
  filter: 'all'
};

// 使用useReducer
function TodoApp() {
  const [state, dispatch] = useReducer(todoReducer, initialState);
  
  const addTodo = (text) => {
    dispatch({ type: 'ADD_TODO', payload: text });
  };
  
  const toggleTodo = (id) => {
    dispatch({ type: 'TOGGLE_TODO', payload: id });
  };
  
  const deleteTodo = (id) => {
    dispatch({ type: 'DELETE_TODO', payload: id });
  };
  
  const setFilter = (filter) => {
    dispatch({ type: 'SET_FILTER', payload: filter });
  };
  
  const filteredTodos = state.todos.filter(todo => {
    if (state.filter === 'active') return !todo.completed;
    if (state.filter === 'completed') return todo.completed;
    return true;
  });
  
  return (
    <div>
      <TodoForm onAdd={addTodo} />
      <TodoFilter filter={state.filter} onFilterChange={setFilter} />
      <TodoList 
        todos={filteredTodos}
        onToggle={toggleTodo}
        onDelete={deleteTodo}
      />
    </div>
  );
}

// 使用useReducer的优势：
// 1. 复杂状态逻辑更容易管理
// 2. 状态更新逻辑集中化
// 3. 更容易测试和调试
// 4. 可以复用reducer逻辑
```

#### Q10: Context API的使用？
**答案：**
```jsx
import { createContext, useContext, useReducer } from 'react';

// 创建Context
const ThemeContext = createContext();
const UserContext = createContext();

// 主题Provider
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };
  
  const value = {
    theme,
    toggleTheme
  };
  
  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// 用户Provider
function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    // 模拟API调用
    setTimeout(() => {
      setUser({ id: 1, name: 'John Doe', email: 'john@example.com' });
      setLoading(false);
    }, 1000);
  }, []);
  
  const login = (userData) => {
    setUser(userData);
  };
  
  const logout = () => {
    setUser(null);
  };
  
  const value = {
    user,
    loading,
    login,
    logout
  };
  
  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
}

// 自定义Hook使用Context
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}

function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within a UserProvider');
  }
  return context;
}

// 使用Context的组件
function Header() {
  const { theme, toggleTheme } = useTheme();
  const { user, logout } = useUser();
  
  return (
    <header className={`header ${theme}`}>
      <h1>My App</h1>
      <div>
        {user ? (
          <div>
            <span>Welcome, {user.name}</span>
            <button onClick={logout}>Logout</button>
          </div>
        ) : (
          <span>Please login</span>
        )}
        <button onClick={toggleTheme}>
          {theme === 'light' ? 'Dark' : 'Light'} Mode
        </button>
      </div>
    </header>
  );
}

// 应用入口
function App() {
  return (
    <ThemeProvider>
      <UserProvider>
        <Header />
        <MainContent />
      </UserProvider>
    </ThemeProvider>
  );
}
```

## 6. 性能优化

### 核心概念
- **React.memo**：组件缓存
- **useMemo**：值缓存
- **useCallback**：函数缓存
- **代码分割**：动态导入
- **虚拟化**：长列表优化
- **懒加载**：组件懒加载

### 常问面试题

#### Q11: React性能优化技巧？
**答案：**
```jsx
import { useState, useMemo, useCallback, memo, lazy, Suspense } from 'react';

// 1. 使用React.memo避免不必要的重新渲染
const ExpensiveComponent = memo(({ data, onUpdate }) => {
  console.log('ExpensiveComponent rendered');
  
  const processedData = useMemo(() => {
    console.log('Processing data...');
    return data.map(item => ({
      ...item,
      processed: item.value * 2
    }));
  }, [data]);
  
  return (
    <div>
      {processedData.map(item => (
        <div key={item.id}>{item.name}: {item.processed}</div>
      ))}
      <button onClick={onUpdate}>Update</button>
    </div>
  );
});

// 2. 使用useCallback缓存函数
function ParentComponent() {
  const [count, setCount] = useState(0);
  const [data, setData] = useState([]);
  
  const handleUpdate = useCallback(() => {
    setData(prev => [...prev, { id: Date.now(), value: Math.random() }]);
  }, []);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <ExpensiveComponent data={data} onUpdate={handleUpdate} />
    </div>
  );
}

// 3. 代码分割和懒加载
const LazyComponent = lazy(() => import('./LazyComponent'));

function App() {
  const [showLazy, setShowLazy] = useState(false);
  
  return (
    <div>
      <button onClick={() => setShowLazy(true)}>
        Load Lazy Component
      </button>
      {showLazy && (
        <Suspense fallback={<div>Loading...</div>}>
          <LazyComponent />
        </Suspense>
      )}
    </div>
  );
}

// 4. 虚拟化长列表
import { FixedSizeList as List } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].name}
    </div>
  );
  
  return (
    <List
      height={600}
      itemCount={items.length}
      itemSize={50}
    >
      {Row}
    </List>
  );
}

// 5. 避免在render中创建对象和函数
function BadComponent({ items }) {
  return (
    <div>
      {items.map(item => (
        <ChildComponent 
          key={item.id}
          style={{ color: 'red' }} // 每次render都创建新对象
          onClick={() => console.log(item.id)} // 每次render都创建新函数
        />
      ))}
    </div>
  );
}

function GoodComponent({ items }) {
  const itemStyle = useMemo(() => ({ color: 'red' }), []);
  
  const handleClick = useCallback((id) => {
    console.log(id);
  }, []);
  
  return (
    <div>
      {items.map(item => (
        <ChildComponent 
          key={item.id}
          style={itemStyle}
          onClick={() => handleClick(item.id)}
        />
      ))}
    </div>
  );
}
```

## 7. 路由管理

### 核心概念
- **React Router**：客户端路由
- **路由配置**：路由定义
- **导航**：程序化导航
- **路由参数**：动态路由
- **嵌套路由**：多级路由

### 常问面试题

#### Q12: React Router的使用？
**答案：**
```jsx
import { 
  BrowserRouter, 
  Routes, 
  Route, 
  Link, 
  useParams, 
  useNavigate,
  useLocation 
} from 'react-router-dom';

// 路由配置
function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
      </nav>
      
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users" element={<Users />} />
        <Route path="/users/:id" element={<UserDetail />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

// 使用路由参数
function UserDetail() {
  const { id } = useParams();
  const navigate = useNavigate();
  const location = useLocation();
  
  const handleBack = () => {
    navigate(-1); // 返回上一页
  };
  
  return (
    <div>
      <h1>User Detail: {id}</h1>
      <p>Current path: {location.pathname}</p>
      <button onClick={handleBack}>Back</button>
    </div>
  );
}

// 嵌套路由
function Users() {
  return (
    <div>
      <h1>Users</h1>
      <Routes>
        <Route index element={<UserList />} />
        <Route path=":id" element={<UserDetail />} />
        <Route path=":id/edit" element={<EditUser />} />
      </Routes>
    </div>
  );
}

// 路由守卫
function ProtectedRoute({ children }) {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  
  return children;
}

// 使用路由守卫
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route path="/dashboard" element={
          <ProtectedRoute>
            <Dashboard />
          </ProtectedRoute>
        } />
      </Routes>
    </BrowserRouter>
  );
}
```

## 8. 错误处理

### 核心概念
- **错误边界**：捕获组件树中的错误
- **Error Boundary**：错误边界组件
- **错误恢复**：错误后的恢复策略
- **错误报告**：错误日志记录

### 常问面试题

#### Q13: 错误边界的实现？
**答案：**
```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
    
    // 发送错误报告
    this.logErrorToService(error, errorInfo);
  }
  
  logErrorToService = (error, errorInfo) => {
    // 发送到错误监控服务
    fetch('/api/errors', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        error: error.toString(),
        errorInfo,
        timestamp: new Date().toISOString()
      })
    });
  };
  
  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>Something went wrong.</h2>
          <details>
            <summary>Error details</summary>
            <pre>{this.state.error && this.state.error.toString()}</pre>
          </details>
          <button onClick={() => this.setState({ hasError: false, error: null })}>
            Try again
          </button>
        </div>
      );
    }
    
    return this.props.children;
  }
}

// 使用错误边界
function App() {
  return (
    <ErrorBoundary>
      <Header />
      <MainContent />
      <Footer />
    </ErrorBoundary>
  );
}

// 函数组件错误处理
function ErrorFallback({ error, resetError }) {
  return (
    <div className="error-fallback">
      <h2>Something went wrong:</h2>
      <pre>{error.message}</pre>
      <button onClick={resetError}>Try again</button>
    </div>
  );
}

// 使用react-error-boundary库
import { ErrorBoundary } from 'react-error-boundary';

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onError={(error, errorInfo) => {
        console.error('Error:', error, errorInfo);
      }}
    >
      <MainContent />
    </ErrorBoundary>
  );
}
```

## 9. 测试

### 核心概念
- **单元测试**：测试单个组件
- **集成测试**：测试组件交互
- **Jest**：测试框架
- **React Testing Library**：组件测试工具
- **Mock**：模拟依赖

### 常问面试题

#### Q14: React组件测试？
**答案：**
```jsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import '@testing-library/jest-dom';
import { Counter } from './Counter';

// 基础组件测试
describe('Counter', () => {
  test('renders counter with initial value', () => {
    render(<Counter initialValue={0} />);
    expect(screen.getByText('Count: 0')).toBeInTheDocument();
  });
  
  test('increments counter when button is clicked', () => {
    render(<Counter initialValue={0} />);
    const button = screen.getByText('Increment');
    
    fireEvent.click(button);
    expect(screen.getByText('Count: 1')).toBeInTheDocument();
  });
  
  test('decrements counter when button is clicked', () => {
    render(<Counter initialValue={1} />);
    const button = screen.getByText('Decrement');
    
    fireEvent.click(button);
    expect(screen.getByText('Count: 0')).toBeInTheDocument();
  });
});

// 异步组件测试
describe('UserProfile', () => {
  test('displays user data after loading', async () => {
    // Mock fetch
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve({
          id: 1,
          name: 'John Doe',
          email: 'john@example.com'
        })
      })
    );
    
    render(<UserProfile userId={1} />);
    
    expect(screen.getByText('Loading...')).toBeInTheDocument();
    
    await waitFor(() => {
      expect(screen.getByText('John Doe')).toBeInTheDocument();
    });
    
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
  });
});

// 自定义Hook测试
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  test('should increment counter', () => {
    const { result } = renderHook(() => useCounter(0));
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
  
  test('should decrement counter', () => {
    const { result } = renderHook(() => useCounter(1));
    
    act(() => {
      result.current.decrement();
    });
    
    expect(result.current.count).toBe(0);
  });
});

// 集成测试
describe('TodoApp', () => {
  test('adds and removes todos', () => {
    render(<TodoApp />);
    
    // 添加todo
    const input = screen.getByPlaceholderText('Add todo');
    const addButton = screen.getByText('Add');
    
    fireEvent.change(input, { target: { value: 'Test todo' } });
    fireEvent.click(addButton);
    
    expect(screen.getByText('Test todo')).toBeInTheDocument();
    
    // 删除todo
    const deleteButton = screen.getByText('Delete');
    fireEvent.click(deleteButton);
    
    expect(screen.queryByText('Test todo')).not.toBeInTheDocument();
  });
});
```

## 10. 最佳实践

### 核心概念
- **组件设计**：单一职责原则
- **状态管理**：合理的状态结构
- **性能优化**：避免不必要的渲染
- **代码组织**：文件结构规范
- **类型安全**：TypeScript使用

### 常问面试题

#### Q15: React开发最佳实践？
**答案：**
```jsx
// 1. 组件设计原则
// 单一职责：每个组件只做一件事
function UserAvatar({ user, size = 'medium' }) {
  return (
    <img 
      src={user.avatar} 
      alt={user.name}
      className={`avatar avatar-${size}`}
    />
  );
}

// 2. 合理的状态结构
function UserProfile() {
  // 相关状态放在一起
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  // 使用useReducer管理复杂状态
  const [profileState, dispatch] = useReducer(profileReducer, initialState);
  
  return (
    <div>
      {loading && <Spinner />}
      {error && <ErrorMessage error={error} />}
      {user && <UserInfo user={user} />}
    </div>
  );
}

// 3. 自定义Hook复用逻辑
function useUser(userId) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    const fetchUser = async () => {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        const userData = await response.json();
        setUser(userData);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    
    fetchUser();
  }, [userId]);
  
  return { user, loading, error };
}

// 4. 错误边界和错误处理
function App() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<LoadingSpinner />}>
        <Router>
          <Routes>
            <Route path="/" element={<Home />} />
            <Route path="/users" element={<Users />} />
          </Routes>
        </Router>
      </Suspense>
    </ErrorBoundary>
  );
}

// 5. 性能优化
const ExpensiveComponent = memo(({ data, onUpdate }) => {
  const processedData = useMemo(() => {
    return data.map(item => ({
      ...item,
      processed: expensiveCalculation(item)
    }));
  }, [data]);
  
  const handleUpdate = useCallback(() => {
    onUpdate();
  }, [onUpdate]);
  
  return (
    <div>
      {processedData.map(item => (
        <Item key={item.id} item={item} />
      ))}
      <button onClick={handleUpdate}>Update</button>
    </div>
  );
});

// 6. 类型安全（TypeScript）
interface UserProps {
  user: {
    id: number;
    name: string;
    email: string;
  };
  onEdit: (user: User) => void;
}

function UserComponent({ user, onEdit }: UserProps) {
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <button onClick={() => onEdit(user)}>Edit</button>
    </div>
  );
}
```

## 总结

React面试重点：
1. **基础概念**：组件化、虚拟DOM、JSX、单向数据流
2. **组件与Props**：函数组件vs类组件、Props传递、类型检查
3. **State与生命周期**：useState、useEffect、生命周期方法
4. **Hooks**：useState、useEffect、自定义Hooks、useMemo、useCallback
5. **状态管理**：useReducer、Context API、状态提升
6. **性能优化**：React.memo、useMemo、useCallback、代码分割
7. **路由管理**：React Router、路由配置、导航、路由守卫
8. **错误处理**：错误边界、错误恢复、错误报告
9. **测试**：单元测试、集成测试、Jest、React Testing Library
10. **最佳实践**：组件设计、状态管理、性能优化、类型安全
