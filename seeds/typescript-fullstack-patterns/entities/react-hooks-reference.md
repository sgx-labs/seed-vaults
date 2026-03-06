---
title: "React Hooks Reference"
tags: [react, hooks, useState, useEffect, useMemo, useCallback, custom-hooks, reference, entity]
content_type: entity
domain: typescript-fullstack
---

# React Hooks Reference

## Built-in Hooks

### State & Refs

| Hook | Purpose | When to Use |
|------|---------|-------------|
| `useState` | Local component state | Toggles, form inputs, UI state |
| `useReducer` | Complex state with actions | State machines, related state transitions |
| `useRef` | Mutable value that persists across renders | DOM refs, previous values, timers |
| `useId` | Generate unique IDs for accessibility | Form labels, ARIA attributes |

### Effects & Lifecycle

| Hook | Purpose | When to Use |
|------|---------|-------------|
| `useEffect` | Side effects after render | API calls, subscriptions, DOM manipulation |
| `useLayoutEffect` | Side effects before paint | Measure DOM, prevent flash |
| `useInsertionEffect` | Insert styles before DOM reads | CSS-in-JS libraries only |

### Performance

| Hook | Purpose | When to Use |
|------|---------|-------------|
| `useMemo` | Cache expensive computations | Filtering/sorting large lists, complex calculations |
| `useCallback` | Cache function references | Passing callbacks to memoized children |
| `useTransition` | Mark updates as non-urgent | Search filtering, tab switching |
| `useDeferredValue` | Defer re-rendering a value | Debouncing expensive renders |

### Context & External

| Hook | Purpose | When to Use |
|------|---------|-------------|
| `useContext` | Read context value | Theme, auth, i18n |
| `useSyncExternalStore` | Subscribe to external store | Zustand, Redux, browser APIs |
| `useActionState` | Track form action state | Server action forms |
| `useOptimistic` | Optimistic UI updates | Show changes before server confirms |
| `useFormStatus` | Form submission status | Disable buttons during submission |
| `use` | Read resources (promises, context) | Data fetching in Server Components |

## Common Custom Hooks

```typescript
// useDebounce — delay a value update
function useDebounce<T>(value: T, delay: number): T {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  return debounced;
}

// useLocalStorage — persist state to localStorage
function useLocalStorage<T>(key: string, initial: T) {
  const [value, setValue] = useState<T>(() => {
    if (typeof window === "undefined") return initial;
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initial;
  });
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);
  return [value, setValue] as const;
}

// useMediaQuery — responsive behavior in JS
function useMediaQuery(query: string): boolean {
  const [matches, setMatches] = useState(false);
  useEffect(() => {
    const media = window.matchMedia(query);
    setMatches(media.matches);
    const listener = (e: MediaQueryListEvent) => setMatches(e.matches);
    media.addEventListener("change", listener);
    return () => media.removeEventListener("change", listener);
  }, [query]);
  return matches;
}

// useOnClickOutside — close dropdowns/modals
function useOnClickOutside(ref: RefObject<HTMLElement>, handler: () => void) {
  useEffect(() => {
    const listener = (event: MouseEvent | TouchEvent) => {
      if (!ref.current || ref.current.contains(event.target as Node)) return;
      handler();
    };
    document.addEventListener("mousedown", listener);
    document.addEventListener("touchstart", listener);
    return () => {
      document.removeEventListener("mousedown", listener);
      document.removeEventListener("touchstart", listener);
    };
  }, [ref, handler]);
}
```

## Rules of Hooks

1. Only call hooks at the top level (no conditionals, loops)
2. Only call hooks from React components or custom hooks
3. Custom hooks must start with `use`

## See Also

- `hub/react-server-components.md` — Hooks are client-only
- `hub/state-management.md` — When to use hooks vs external stores
- `hub/performance-patterns.md` — Performance hooks
