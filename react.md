## 1. What is React?
**Interview answer:** React is a JavaScript library for building user interfaces from reusable components. It describes the UI as a function of state and props. React reconciles changes and updates the necessary parts of the DOM.

## 2. Components, props, and state
- **Component:** reusable UI unit.
- **Props:** read-only inputs passed from a parent.
- **State:** component-owned data that can change over time.
- Keep components focused and use descriptive names.

```jsx
import { useState } from "react";

function Greeting({ initialName = "Guest" }) {
  const [name, setName] = useState(initialName);

  return (
    <section>
      <h2>Hello, {name}!</h2>
      <button onClick={() => setName("Abhi")}>Set name</button>
    </section>
  );
}

export default Greeting;
```

**How it works:** `initialName` is a prop; `name` is state. Calling `setName` schedules a render with the new state.

## 3. Why use keys in lists?
Keys help React match list items between renders. Use stable IDs rather than array indexes when items can be inserted, removed, or reordered.

```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

## 4. Controlled vs uncontrolled inputs
A controlled input gets its value from React state. An uncontrolled input keeps its current value in the DOM and can be read with a ref.

```jsx
import { useState } from "react";

function SearchBox() {
  const [query, setQuery] = useState("");

  return (
    <input
      value={query}
      onChange={(event) => setQuery(event.target.value)}
      placeholder="Search"
    />
  );
}
```

## 5. Virtual DOM and reconciliation
The Virtual DOM is a useful mental model for React's in-memory description of UI. Reconciliation is the process of comparing the next UI description with the previous one and determining updates. Avoid claiming React always updates only one DOM node; actual work depends on component structure and changes.

## 6. Common interview questions
**Q: Props vs state?** Props come from a parent and should not be mutated by the receiving component; state is managed by a component or a state-owning hook.

**Q: Why should state not be mutated directly?** React relies on state updates to schedule renders, and mutation can cause stale or unpredictable UI. Create a new object/array instead.

**Q: Why are array indexes risky as keys?** After insertions or reordering, an index can refer to a different logical item, potentially causing state or DOM reuse issues.

## Quick revision
- Components compose the UI.
- Props pass data down; state represents changing data.
- State updates schedule rendering.
- Use stable keys for dynamic lists.
- Avoid direct state mutation.
"""

files["01-React/02-hooks-and-state.md"] = r"""
# React Hooks and State

## 1. Rules of Hooks
Call Hooks at the top level of React function components or custom Hooks. Do not call them inside loops, conditions, or nested functions. This lets React preserve Hook call order.

## 2. useState
Use it for local state. When next state depends on previous state, use the functional update form.

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  function incrementTwice() {
    setCount((current) => current + 1);
    setCount((current) => current + 1);
  }

  return <button onClick={incrementTwice}>Count: {count}</button>;
}
```

**Interview answer:** The functional updater receives the latest queued state, so it is safer when multiple updates depend on the previous value.

## 3. useEffect
Use Effects to synchronize with external systems such as subscriptions, timers, or browser APIs—not as a default place for all derived calculations.

```jsx
import { useEffect, useState } from "react";

function UserStatus({ userId }) {
  const [status, setStatus] = useState("Loading");

  useEffect(() => {
    const controller = new AbortController();

    async function loadUser() {
      try {
        const response = await fetch(`/api/users/${userId}`, {
          signal: controller.signal,
        });
        if (!response.ok) throw new Error("Request failed");
        const user = await response.json();
        setStatus(user.active ? "Active" : "Inactive");
      } catch (error) {
        if (error.name !== "AbortError") setStatus("Could not load user");
      }
    }

    loadUser();
    return () => controller.abort();
  }, [userId]);

  return <p>{status}</p>;
}
```

**Dependency array:**
- No array: Effect runs after every committed render.
- `[]`: Effect setup runs on mount; cleanup on unmount (development Strict Mode may run an extra setup/cleanup cycle).
- `[userId]`: reruns when `userId` changes.

## 4. useRef
Stores a mutable value that persists across renders without causing a render when changed. Commonly used for DOM references or timer IDs.

```jsx
import { useRef } from "react";

function FocusInput() {
  const inputRef = useRef(null);

  return (
    <>
      <input ref={inputRef} />
      <button onClick={() => inputRef.current?.focus()}>Focus</button>
    </>
  );
}
```

## 5. useMemo vs useCallback
- `useMemo` caches a calculated value between renders when dependencies are unchanged.
- `useCallback` caches a function definition between renders when dependencies are unchanged.
- Neither should be added everywhere; measure performance and understand dependencies.

```jsx
const filteredUsers = useMemo(
  () => users.filter((user) => user.name.includes(query)),
  [users, query]
);

const handleSelect = useCallback((id) => {
  setSelectedId(id);
}, []);
```

## 6. Context
Context lets components read values from a provider without passing props through every intermediate component. It is useful for values such as theme or authenticated-user information. Frequently changing global state may be better handled with a dedicated state solution or localized state.

## 7. Common questions
**Q: Does useEffect run before paint?** Usually Effects run after the browser paints, but timing can vary by interaction. `useLayoutEffect` runs after DOM mutations and before paint; use it only when layout measurement/visual synchronization requires it.

**Q: Why does an Effect run twice in development?** React Strict Mode may perform an extra setup/cleanup cycle to expose unsafe side effects. It is not the normal production behavior.

**Q: When do you need cleanup?** When setup creates a subscription, timer, event listener, connection, or request that should be stopped or cancelled.

## Quick revision
- Follow Hook rules.
- Use functional state updates for previous-state calculations.
- Effects synchronize with external systems.
- Refs persist mutable values without rerendering.
- Memoization is an optimization, not a default requirement.
"""

files["01-React/03-rendering-performance.md"] = r"""
# React Rendering and Performance

## 1. What causes a component to render?
A component can render when its state changes, its parent renders, or a context value it consumes changes. A render does not necessarily mean every DOM node is changed.

## 2. React.memo
`React.memo` can skip rendering a component when its props compare equal to their previous values. It is most useful when rendering is expensive and props are stable.

```jsx
import { memo } from "react";

const UserCard = memo(function UserCard({ user }) {
  return <article>{user.name}</article>;
});
```

If the parent creates a new object on every render, shallow prop comparison sees a new reference. `useMemo` can stabilize a value when there is a demonstrated need.

## 3. Performance checklist
1. Measure with React DevTools Profiler.
2. Avoid unnecessary state at high levels of the tree.
3. Keep state close to the components that use it.
4. Use stable keys.
5. Memoize only when profiling or architecture supports it.
6. Use list virtualization for very large lists.
7. Split bundles with lazy loading when appropriate.
8. Optimize network requests and cache server data appropriately.

## 4. Lazy loading
```jsx
import { lazy, Suspense } from "react";

const ReportsPage = lazy(() => import("./ReportsPage"));

export default function App() {
  return (
    <Suspense fallback={<p>Loading reports…</p>}>
      <ReportsPage />
    </Suspense>
  );
}
```

## 5. Debouncing search
Debouncing waits until input activity pauses before invoking work. Cleanup prevents an old timer from firing.

```jsx
import { useEffect, useState } from "react";

function Search() {
  const [query, setQuery] = useState("");
  const [debouncedQuery, setDebouncedQuery] = useState("");

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedQuery(query), 300);
    return () => clearTimeout(timer);
  }, [query]);

  useEffect(() => {
    if (!debouncedQuery.trim()) return;
    // Fetch or filter using debouncedQuery.
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={(event) => setQuery(event.target.value)}
      placeholder="Search users"
    />
  );
}
```

## 6. Common questions
**Q: useMemo vs React.memo?** `useMemo` caches a value; `React.memo` can skip a component render when props are equal.

**Q: What is code splitting?** Splitting a bundle into smaller chunks that can be loaded when needed.

**Q: How do you investigate a slow React page?** Reproduce the problem, profile renders, identify expensive components/calculations and network work, make a targeted change, and measure again.

## Quick revision
- Profile before optimizing.
- Rendering and DOM mutation are different.
- Memoization has a cost and dependency considerations.
- Lazy loading can reduce initial JavaScript.
- Debounce user input when repeated work is unnecessary.
"""

files["01-React/04-advanced-react.md"] = r"""
# Advanced React Interview Topics

## 1. Lifting state up
Move shared state to the closest common parent of the components that need it. Pass the value and event handlers down as props.

## 2. Custom Hooks
A custom Hook packages reusable stateful logic. Its name starts with `use`.

```jsx
import { useEffect, useState } from "react";

function useOnlineStatus() {
  const [online, setOnline] = useState(navigator.onLine);

  useEffect(() => {
    const goOnline = () => setOnline(true);
    const goOffline = () => setOnline(false);

    window.addEventListener("online", goOnline);
    window.addEventListener("offline", goOffline);
    return () => {
      window.removeEventListener("online", goOnline);
      window.removeEventListener("offline", goOffline);
    };
  }, []);

  return online;
}
```

## 3. Error boundaries
Error boundaries catch rendering errors in descendant components and show fallback UI. Traditional error boundaries do not catch event-handler errors, most asynchronous errors, or errors thrown by the boundary itself. Frameworks may provide additional handling.

## 4. CSR, SSR, and SSG
- **CSR:** browser JavaScript renders the UI.
- **SSR:** HTML is rendered on the server for a request, then often hydrated in the browser.
- **SSG:** HTML is generated ahead of time at build time.
- **ISR:** a framework-specific approach that can regenerate static output after deployment.
Choose based on SEO, initial rendering, freshness, infrastructure, and interaction requirements.

## 5. Authentication and protected UI
The frontend can hide or show UI based on session state, but authorization must be enforced by the backend on every protected operation. Never trust a role or permission merely because it exists in client-side state.

## 6. Testing
- Unit/component tests: verify component behavior and accessible interactions.
- Integration tests: verify components and data flow together.
- End-to-end tests: verify key user journeys in a browser.
Prefer testing observable behavior over internal implementation details.

## 7. Common questions
**Q: What is hydration?** React attaches client-side behavior to server-rendered HTML.

**Q: Is hiding a button sufficient authorization?** No. The server must verify identity and permissions for every protected request.

**Q: When would you use Context?** For values needed across a subtree, such as theme or current-user information, while considering update frequency and rerender impact.
"""

files["01-React/05-react-interview-questions.md"] = r"""
# React Interview Questions and Short Answers

1. **What is React?** A JavaScript library for composing interactive user interfaces from components.
2. **Props vs state?** Props are inputs; state is changing data managed by a component or state-owning hook.
3. **What is reconciliation?** The process React uses to determine how the UI should update after a render.
4. **Why use keys?** To match list items across renders.
5. **What are Hooks?** Functions that let function components use React features such as state and Effects.
6. **What is a stale closure?** A function capturing values from an earlier render.
7. **What is controlled input?** An input whose value is driven by React state.
8. **What is lifting state up?** Moving shared state to a common ancestor.
9. **useRef vs useState?** Ref changes do not trigger rendering; state updates can trigger rendering.
10. **useMemo vs useCallback?** Cache a computed value vs cache a function definition.
11. **What is React.memo?** A component memoization optimization based on prop comparison.
12. **What is Context?** A way to provide values to descendants without manually passing props through every level.
13. **What is an error boundary?** A component boundary that displays fallback UI for caught descendant rendering errors.
14. **What is lazy loading?** Loading code or resources when needed rather than all at initial load.
15. **What is hydration?** Connecting client-side React behavior to server-rendered HTML.
16. **How do you optimize a large list?** Profile, reduce unnecessary renders, and consider virtualization.
17. **Why should state be immutable?** New references make changes easier to detect and prevent mutation-related bugs.
18. **What does Strict Mode do?** Adds development checks that help reveal unsafe patterns.
19. **How do you cancel a fetch in an Effect?** Use `AbortController` and abort it in cleanup.
20. **How do you secure a protected API?** Enforce authentication and authorization on the server; frontend guards are only UX.
"""
