# Introduction to React

**Component-Based UI Development**

JSX | components | hooks | state | virtual DOM | ecosystem

---

## Table of Contents

1. [Agenda](#slide-02--agenda)
2. [What Is React?](#slide-03--what-is-react)
3. [The Virtual DOM](#slide-04--the-virtual-dom)
4. [JSX — JavaScript XML](#slide-05--jsx--javascript-xml)
5. [Components](#slide-06--components)
6. [Props & Data Flow](#slide-07--props--data-flow)
7. [State & useState](#slide-08--state--usestate)
8. [useEffect & Side Effects](#slide-09--useeffect--side-effects)
9. [Event Handling](#slide-10--event-handling)
10. [Conditional Rendering & Lists](#slide-11--conditional-rendering--lists)
11. [Forms & Controlled Components](#slide-12--forms--controlled-components)
12. [Context API & useContext](#slide-13--context-api--usecontext)
13. [useReducer & Complex State](#slide-14--usereducer--complex-state)
14. [Custom Hooks](#slide-15--custom-hooks)
15. [React Router](#slide-16--react-router)
16. [Performance Optimisation](#slide-17--performance-optimisation)
17. [Testing React Components](#slide-18--testing-react-components)
18. [The React Ecosystem](#slide-19--the-react-ecosystem)
19. [Summary & Next Steps](#slide-20--summary--next-steps)

---

## Slide 02 — Agenda

### Foundations
- What Is React?
- The Virtual DOM
- JSX — JavaScript XML
- Components
- Props & Data Flow

### Core Hooks & Patterns
- State & useState
- useEffect & Side Effects
- Event Handling
- Conditional Rendering & Lists
- Forms & Controlled Components

### Advanced State & Logic
- Context API & useContext
- useReducer & Complex State
- Custom Hooks

### Ecosystem & Production
- React Router
- Performance Optimisation
- Testing React Components
- The React Ecosystem
- Summary & Next Steps

---

## Slide 03 — What Is React?

React is a **declarative**, **component-based** JavaScript library for building user interfaces. Created at Facebook by Jordan Walke in 2011, open-sourced in **May 2013**.

### Key Properties
- Declarative rendering
- Component architecture
- Unidirectional data flow
- Virtual DOM diffing
- JSX syntax extension

### Timeline
- **2011** — Internal use at Facebook
- **2013** — Open-sourced
- **2015** — React Native released
- **2019** — Hooks (v16.8)
- **2022** — React 18 (concurrent rendering)

### Who Uses React?
- Meta (Facebook, Instagram)
- Netflix, Airbnb, Uber
- Twitter / X, Discord
- Shopify, Dropbox
- Most popular UI library on npm

React is **not a framework** — it handles only the view layer. You choose your own routing, state management, and build tools. This flexibility is both its greatest strength and source of decision fatigue.

---

## Slide 04 — The Virtual DOM

React maintains a lightweight **in-memory representation** of the real DOM. When state changes, React builds a new virtual tree, **diffs** it against the previous one, and applies only the minimal set of real DOM mutations.

### Reconciliation Algorithm
- Elements of **different types** produce different trees — full subtree replacement
- Elements of the **same type** — React updates only changed attributes
- Children are compared using **keys** for efficient reordering
- O(n) heuristic diffing instead of O(n³) tree comparison

### Fiber Architecture (React 16+)
- Rewrite of the reconciler as an **incremental** rendering engine
- Work is split into units called **fibers**
- Supports **priority-based scheduling** — urgent updates (input) interrupt lower-priority work
- Enables concurrent rendering in React 18

**Pipeline:** State Change → New vDOM Tree → Diff / Reconcile → Patch Real DOM

---

## Slide 05 — JSX — JavaScript XML

JSX is a **syntax extension** that lets you write HTML-like markup inside JavaScript. It compiles to `React.createElement()` calls (or the new JSX transform in React 17+).

### JSX Syntax

```jsx
const element = (
  <div className="greeting">
    <h1>Hello, {user.name}!</h1>
    <p>You have {unread} messages.</p>
  </div>
);
```

### Expressions & Conditional Rendering

```jsx
// Embed any JS expression with { }
const list = (
  <ul>
    {items.map(item => (
      <li key={item.id}>{item.name}</li>
    ))}
  </ul>
);

// Conditional rendering
function Status({ isOnline }) {
  return (
    <span>
      {isOnline ? '🟢 Online' : '⚪ Offline'}
    </span>
  );
}
```

### Key JSX vs HTML Differences

| HTML | JSX Equivalent | Reason |
|------|----------------|--------|
| `class` | `className` | `class` is a reserved word in JS |
| `for` | `htmlFor` | `for` is a reserved word in JS |
| `style="color:red"` | `style={{color: 'red'}}` | Style accepts an object, not a string |
| `onclick` | `onClick` | camelCase event handlers |

---

## Slide 06 — Components

Components are the **building blocks** of every React UI. They accept inputs (props), manage internal state, and return JSX describing what should appear on screen.

### Functional Component (Modern)

```jsx
function UserCard({ name, role, avatar }) {
  return (
    <div className="user-card">
      <img src={avatar} alt={name} />
      <h2>{name}</h2>
      <span className="role">{role}</span>
    </div>
  );
}

// Arrow function variant
const Badge = ({ label, color }) => (
  <span style={{ background: color }}>
    {label}
  </span>
);
```

### Class Component (Legacy)

```jsx
class UserCard extends React.Component {
  render() {
    const { name, role, avatar } = this.props;
    return (
      <div className="user-card">
        <img src={avatar} alt={name} />
        <h2>{name}</h2>
        <span>{role}</span>
      </div>
    );
  }
}
```

### Composition Over Inheritance

React favours **composition**: build complex UIs by nesting simple components. Use the `children` prop for generic containers and render props or hooks for shared logic. Class inheritance is almost never needed.

---

## Slide 07 — Props & Data Flow

Props are **read-only** inputs passed from parent to child. React enforces **one-way (top-down) data flow**, making the application easier to reason about and debug.

```jsx
// Parent passes props down
function App() {
  return (
    <Dashboard
      user={{ name: 'Alice', id: 42 }}
      theme="dark"
      onLogout={() => auth.signOut()}
    />
  );
}

// Child receives and uses props
function Dashboard({ user, theme, onLogout }) {
  return (
    <div className={`dash ${theme}`}>
      <Header name={user.name} />
      <button onClick={onLogout}>Log Out</button>
    </div>
  );
}
```

### PropTypes (Runtime Checks)

```jsx
import PropTypes from 'prop-types';

Dashboard.propTypes = {
  user:     PropTypes.shape({
    name: PropTypes.string.isRequired,
    id:   PropTypes.number,
  }),
  theme:    PropTypes.oneOf(['light', 'dark']),
  onLogout: PropTypes.func.isRequired,
};
```

### children Prop

```jsx
function Panel({ title, children }) {
  return (
    <section className="panel">
      <h2>{title}</h2>
      {children}
    </section>
  );
}

<Panel title="Settings">
  <ToggleSwitch />
</Panel>
```

---

## Slide 08 — State & useState

State is **mutable data** that belongs to a component. When state changes, React re-renders the component. The `useState` hook is the primary way to add local state to a function component.

### Basic useState

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
      <button onClick={() => setCount(0)}>
        Reset
      </button>
    </div>
  );
}
```

### Functional Updates & Objects

```jsx
// Functional update (prev state)
setCount(prev => prev + 1);

// Object state — spread to preserve fields
const [form, setForm] = useState({
  name: '', email: '', age: 0
});

function updateName(name) {
  setForm(prev => ({ ...prev, name }));
}

// Array state — immutable patterns
const [items, setItems] = useState([]);
const addItem = (item) =>
  setItems(prev => [...prev, item]);
const removeItem = (id) =>
  setItems(prev => prev.filter(i => i.id !== id));
```

### Batching (React 18+)

React 18 automatically batches **all** state updates — including those in promises, timeouts, and event handlers — into a single re-render for better performance. In React 17, only synthetic event handlers were batched.

---

## Slide 09 — useEffect & Side Effects

`useEffect` lets you run **side effects** in function components — data fetching, subscriptions, DOM mutations, and timers. It replaces `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.

### Dependency Array Patterns

```jsx
// Runs after EVERY render
useEffect(() => { /* ... */ });

// Runs ONCE on mount
useEffect(() => {
  fetchData();
}, []);

// Runs when `userId` changes
useEffect(() => {
  fetchUser(userId);
}, [userId]);

// Cleanup function (unmount / before re-run)
useEffect(() => {
  const ws = new WebSocket(url);
  ws.onmessage = handleMessage;
  return () => ws.close(); // cleanup
}, [url]);
```

### Data Fetching Example

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);

    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        if (!cancelled) {
          setUser(data);
          setLoading(false);
        }
      });

    return () => { cancelled = true; };
  }, [userId]);

  if (loading) return <Spinner />;
  return <h1>{user.name}</h1>;
}
```

**Common mistake:** forgetting dependencies causes stale closures; adding object literals as deps causes infinite loops. Use the `eslint-plugin-react-hooks` exhaustive-deps rule.

---

## Slide 10 — Event Handling

React wraps native DOM events in **SyntheticEvent** objects for cross-browser consistency. Events are **delegated** to the root container (React 17+) rather than individual DOM nodes.

### Common Patterns

```jsx
function SearchBar() {
  const [query, setQuery] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    search(query);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      <button type="submit">Go</button>
    </form>
  );
}
```

### Passing Arguments & Memoisation

```jsx
function TodoList({ todos, onDelete }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          {todo.text}
          <button onClick={() => onDelete(todo.id)}>
            Delete
          </button>
        </li>
      ))}
    </ul>
  );
}

// Memoised handler (avoid re-renders)
const handleClick = useCallback(
  (id) => dispatch({ type: 'DELETE', id }),
  [dispatch]
);
```

### Key Events

| Event | React Prop | When Fires |
|-------|-----------|------------|
| click | `onClick` | Element is clicked |
| change | `onChange` | Input value changes (fires on every keystroke in React) |
| submit | `onSubmit` | Form is submitted |
| keydown | `onKeyDown` | A key is pressed |
| focus / blur | `onFocus` / `onBlur` | Element gains / loses focus |

---

## Slide 11 — Conditional Rendering & Lists

### Conditional Patterns

```jsx
// Ternary operator
{isLoggedIn ? <Dashboard /> : <Login />}

// Logical AND (short-circuit)
{hasError && <ErrorBanner msg={error} />}

// Early return
function Page({ user }) {
  if (!user) return <Redirect to="/login" />;
  return <Profile user={user} />;
}

// Switch-like with object map
const STATUS_ICONS = {
  success: <CheckIcon />,
  error:   <AlertIcon />,
  loading: <Spinner />,
};
return STATUS_ICONS[status] ?? null;
```

### Rendering Lists

```jsx
function ProductList({ products }) {
  if (products.length === 0) {
    return <p>No products found.</p>;
  }

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>
          <h3>{product.name}</h3>
          <span>${product.price}</span>
        </li>
      ))}
    </ul>
  );
}
```

### Why Keys Matter

Keys help React **identify which items changed**. Use a **stable, unique id** — never use array index as key when items can be reordered, added, or removed. Bad keys cause subtle bugs: wrong inputs retain values, animations break, and components lose state.

---

## Slide 12 — Forms & Controlled Components

### Controlled Component

```jsx
function SignupForm() {
  const [form, setForm] = useState({
    email: '', password: '', agree: false
  });
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setForm(prev => ({
      ...prev,
      [name]: type === 'checkbox' ? checked : value,
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const errs = validate(form);
    if (Object.keys(errs).length) {
      setErrors(errs);
    } else {
      submitForm(form);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" value={form.email}
        onChange={handleChange} />
      {errors.email && <span>{errors.email}</span>}
      <input name="password" type="password"
        value={form.password}
        onChange={handleChange} />
      <label>
        <input name="agree" type="checkbox"
          checked={form.agree}
          onChange={handleChange} />
        I agree to terms
      </label>
      <button type="submit">Sign Up</button>
    </form>
  );
}
```

### Uncontrolled (useRef)

```jsx
function QuickSearch() {
  const inputRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    search(inputRef.current.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={inputRef} defaultValue="" />
      <button type="submit">Go</button>
    </form>
  );
}
```

### Controlled vs Uncontrolled

| Aspect | Controlled | Uncontrolled |
|--------|-----------|-------------|
| Source of truth | React state | DOM |
| Validation | On every change | On submit |
| Dynamic input | Easy | Harder |
| Performance | More re-renders | Fewer re-renders |

---

## Slide 13 — Context API & useContext

Context provides a way to pass data through the component tree **without prop drilling**. Ideal for global concerns: theme, auth, locale, feature flags.

### Creating & Providing

```jsx
import { createContext, useState, useContext } from 'react';

const ThemeContext = createContext('light');

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  const toggle = () =>
    setTheme(t => t === 'light' ? 'dark' : 'light');

  return (
    <ThemeContext.Provider value={{ theme, toggle }}>
      {children}
    </ThemeContext.Provider>
  );
}
```

### Consuming Context

```jsx
function ThemeToggle() {
  const { theme, toggle } = useContext(ThemeContext);

  return (
    <button onClick={toggle}>
      Current: {theme}
    </button>
  );
}
```

### Caveat: Re-renders

**Every consumer re-renders** when the context value changes. Split contexts by update frequency (e.g., separate ThemeContext from UserContext). Use `useMemo` on the value object to avoid unnecessary reference changes.

---

## Slide 14 — useReducer & Complex State

`useReducer` is an alternative to `useState` for **complex state logic**. Inspired by Redux, it centralises state transitions in a pure **reducer function**.

### Todo App with useReducer

```jsx
const initialState = { todos: [], nextId: 1 };

function reducer(state, action) {
  switch (action.type) {
    case 'ADD':
      return {
        ...state,
        todos: [...state.todos, {
          id: state.nextId, text: action.text, done: false
        }],
        nextId: state.nextId + 1,
      };
    case 'TOGGLE':
      return {
        ...state,
        todos: state.todos.map(t =>
          t.id === action.id ? { ...t, done: !t.done } : t
        ),
      };
    case 'DELETE':
      return {
        ...state,
        todos: state.todos.filter(t => t.id !== action.id),
      };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}
```

### Using the Reducer

```jsx
function TodoApp() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <AddTodoForm onAdd={(text) =>
        dispatch({ type: 'ADD', text })} />
      {state.todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={() => dispatch({ type: 'TOGGLE', id: todo.id })}
          onDelete={() => dispatch({ type: 'DELETE', id: todo.id })}
        />
      ))}
    </div>
  );
}
```

### useState vs useReducer
- **useState** — simple values, few transitions
- **useReducer** — complex objects, many action types, testable transitions
- Combine with **Context** to replace Redux for mid-size apps

---

## Slide 15 — Custom Hooks

Custom hooks let you **extract and reuse stateful logic** across components. A custom hook is simply a function whose name starts with `use` and that calls other hooks.

### useLocalStorage

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
const [theme, setTheme] = useLocalStorage('theme', 'dark');
```

### useFetch

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);
    fetch(url)
      .then(r => r.json())
      .then(d => {
        if (!cancelled) { setData(d); setLoading(false); }
      })
      .catch(e => {
        if (!cancelled) { setError(e); setLoading(false); }
      });
    return () => { cancelled = true; };
  }, [url]);

  return { data, loading, error };
}

// Usage
const { data, loading } = useFetch('/api/posts');
```

### Rules of Hooks
- **Only call hooks at the top level** — never inside loops, conditions, or nested functions
- **Only call hooks from React functions** — function components or custom hooks
- Use the `eslint-plugin-react-hooks` plugin to enforce these rules automatically

---

## Slide 16 — React Router

**React Router v6** is the standard routing library for React SPAs. It enables client-side navigation without full-page reloads, with nested layouts, dynamic segments, and data loading.

### Basic Setup

```jsx
import { BrowserRouter, Routes, Route, Link }
  from 'react-router-dom';

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
        <Route path="/users" element={<Users />}>
          <Route path=":userId" element={<UserDetail />} />
        </Route>
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Params, Loaders & Navigation

```jsx
import { useParams, useNavigate } from 'react-router-dom';

function UserDetail() {
  const { userId } = useParams();
  const navigate = useNavigate();
  const { data: user } = useFetch(`/api/users/${userId}`);

  return (
    <div>
      <button onClick={() => navigate(-1)}>Back</button>
      <h1>{user?.name}</h1>
    </div>
  );
}

// Route loader (React Router 6.4+)
const userLoader = async ({ params }) => {
  const res = await fetch(`/api/users/${params.userId}`);
  if (!res.ok) throw new Response('', { status: 404 });
  return res.json();
};
```

**Nested routes** render inside an `<Outlet />` component in the parent, enabling persistent layouts (e.g., sidebar + content).

---

## Slide 17 — Performance Optimisation

### React.memo

```jsx
const UserCard = React.memo(({ name, avatar }) => {
  return (
    <div className="card">
      <img src={avatar} alt={name} />
      <h3>{name}</h3>
    </div>
  );
});
```

### useMemo & useCallback

```jsx
// Memoise expensive computation
const sorted = useMemo(
  () => items.slice().sort((a, b) => a.price - b.price),
  [items]
);

// Memoise callback identity
const handleDelete = useCallback(
  (id) => dispatch({ type: 'DELETE', id }),
  [dispatch]
);
```

### Code Splitting (lazy + Suspense)

```jsx
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./Dashboard'));

function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <Dashboard />
    </Suspense>
  );
}
```

### Quick Wins Checklist
- Use **React DevTools Profiler** to find slow components
- Add **stable keys** to list items
- Avoid **inline object / array literals** as props
- Split context by **update frequency**
- Use **lazy()** for route-level code splitting
- Virtualise long lists with `react-window`
- Use `useTransition` for non-urgent updates (React 18)

---

## Slide 18 — Testing React Components

The **React Testing Library** (RTL) encourages testing components the way users interact with them — by visible text, roles, and labels rather than implementation details.

### Unit Test with RTL + Jest

```jsx
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

test('increments counter on click', () => {
  render(<Counter />);

  const button = screen.getByRole('button', { name: /increment/i });
  const display = screen.getByText(/count: 0/i);

  fireEvent.click(button);

  expect(screen.getByText(/count: 1/i)).toBeInTheDocument();
});
```

### Async & Mocking

```jsx
import { render, screen, waitFor } from '@testing-library/react';
import UserProfile from './UserProfile';

global.fetch = jest.fn(() =>
  Promise.resolve({
    json: () => Promise.resolve({ name: 'Alice', role: 'Engineer' }),
  })
);

test('loads and displays user', async () => {
  render(<UserProfile userId={42} />);

  expect(screen.getByText(/loading/i)).toBeInTheDocument();

  await waitFor(() => {
    expect(screen.getByText('Alice')).toBeInTheDocument();
  });
});
```

### Best Practices
- Query by **role**, **label**, or **text** — avoid test IDs unless necessary
- Use `userEvent` over `fireEvent` for realistic interaction simulation
- Test **behaviour, not implementation** — do not assert on state or internal methods
- Use **MSW** (Mock Service Worker) for network mocking in integration tests

---

## Slide 19 — The React Ecosystem

### Frameworks
- **Next.js** — SSR, SSG, API routes, App Router
- **Remix** — full-stack with nested routes & loaders
- **Gatsby** — static site generation with GraphQL
- **Astro** — islands architecture, multi-framework

### State Management
- **Redux Toolkit** — predictable, middleware-friendly
- **Zustand** — lightweight, minimal boilerplate
- **Jotai / Recoil** — atomic state model
- **TanStack Query** — server state & caching

### UI Libraries
- **Material UI (MUI)** — Google Material Design
- **Chakra UI** — accessible, composable
- **shadcn/ui** — copy-paste Radix + Tailwind
- **Ant Design** — enterprise-grade components

### Data Fetching & Forms
- **TanStack Query** — caching, background refetch, pagination
- **SWR** — stale-while-revalidate by Vercel
- **React Hook Form** — performant forms with minimal re-renders
- **Zod** — TypeScript-first schema validation

### Build Tools & Utilities
- **Vite** — fast dev server & bundler (replaces CRA)
- **TypeScript** — static typing for React props & state
- **Storybook** — component development in isolation
- **Playwright / Cypress** — end-to-end testing

---

## Slide 20 — Summary & Next Steps

### What We Covered
- React's **component model** and virtual DOM
- **JSX** syntax and expressions
- **Props**, one-way data flow, children
- **Hooks**: useState, useEffect, useReducer, useContext
- Event handling, forms, conditional rendering
- Custom hooks for reusable logic
- Routing, performance, testing, ecosystem

### Recommended Next Steps
- Build a project: Todo app, blog, e-commerce dashboard
- Add **TypeScript** to your React projects
- Learn a meta-framework: **Next.js** or **Remix**
- Master **TanStack Query** for data fetching
- Study React Server Components & the App Router
- Write tests from day one with **RTL**

### Essential Resources
- **react.dev** — Official documentation (completely rewritten in 2023)
- **React DevTools** — Browser extension for debugging components & performance
- **Epic React** by Kent C. Dodds — Comprehensive workshop series
- **TanStack** — Query, Router, Table, and more by Tanner Linsley

---

*Thank you! — Built with Reveal.js · Single self-contained HTML file*
