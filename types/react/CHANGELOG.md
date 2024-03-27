# Changelog

The goal of this types release is to reduce the type-specific API surface that contributed to a steeper learning curve for React with regards to TypeScript usage.
This effort is certainly not finished but we tried to make not too many breaking changes that would impact the React 19 migration too negatively. The runtime changes are far more important since they directly affect the end user of your application.

Most of the changes should be codemoddable. Check out the migration guide below for the recommended migration strategy with regards to types.

## Install (before React 19.0.0 is released)

The `@types/*` packages do not support pre-releases. Instead, you have to alias React types packages to other packages. Modern package managers support this via `resolutions` or `overrides`.

Yarn and PNPM:
```json
{
  "dependencies": {
    "@types/react": "npm:types-react@alpha",
    "@types/react-dom": "npm:types-react-dom@alpha"
  },
  "resolutions": {
    "@types/react": "npm:types-react@alpha",
    "@types/react-dom": "npm:types-react-dom@alpha"
  }
}
```
NPM:
```json
{
  "dependencies": {
    "@types/react": "npm:types-react@alpha",
    "@types/react-dom": "npm:types-react-dom@alpha"
  },
  "overrides": {
    "@types/react": "npm:types-react@alpha",
    "@types/react-dom": "npm:types-react-dom@alpha"
  }
}
```

The following types packages have alphas under different packages:
- `@types/react` -> `types-react`
- `@types/react-dom` -> `types-react-dom`
- `@types/react-is` -> `types-react-is`
- `@types/react-test-renderer` -> `types-react-test-renderer`
- `@types/scheduler` -> `types-scheduler`
- `@types/use-sync-external-store` -> `types-use-sync-external-store`

## Breaking changes

This section focuses on breaking changes of the React types. We won't discuss how to best migrate runtime APIs like `createFactory` or `ReactDOMTestUtils.renderIntoDocument()`.

### Removed types

### Codemoddable

- global `JSX` namespace: [Codemoddable with `scoped-jx`](https://github.com/eps1lon/types-react-codemod#scoped-jsx). Use `React.JSX` instead.
- `ReactChild`: [Codemoddable with `deprecated-react-child`](https://github.com/eps1lon/types-react-codemod#deprecated-react-child). Use ? instead.
- `ReactFragment`: [Codemoddable with `deprecated-react-framgment`](https://github.com/eps1lon/types-react-codemod#deprecated-react-framgment). Use ? instead.
- `ReactNodeArray`: [Codemoddable with `deprecated-react-node-array`](https://github.com/eps1lon/types-react-codemod#deprecated-react-node-array). Use ? instead.
- `ReactText`: [Codemoddable with `deprecated-react-text`](https://github.com/eps1lon/types-react-codemod#deprecated-react-text). Use `number | string | bigint` instead.
- `Requireable`: [Codemoddable with `deprecated-prop-types-types`](https://github.com/eps1lon/types-react-codemod#deprecated-prop-types-types). Use `Requireable` from `prop-types` instead.
- `ValidationMap`: [Codemoddable with `deprecated-prop-types-types`](https://github.com/eps1lon/types-react-codemod#deprecated-prop-types-types). Use `ValidationMap` from `prop-types` instead.
- `Validator`: [Codemoddable with `deprecated-prop-types-types`](https://github.com/eps1lon/types-react-codemod#). Use `Validator` from `prop-types` instead.
- `VoidFunctionComponent`: [Codemoddable with `deprecated-void-function-component`](https://github.com/eps1lon/types-react-codemod#deprecated-void-function-component). Use `FunctionComponent` instead.
- `VFC`: [Codemoddable with `deprecated-void-function-component`](https://github.com/eps1lon/types-react-codemod#). Use `FC` instead.
- `WeakValidationMap`: [Codemoddable with `deprecated-prop-types-types`](https://github.com/eps1lon/types-react-codemod#deprecated-prop-types-tpyes). Use `WeakValidationMap` from `prop-types` instead.


### Not codemoddable

These types were not used at all in the [example migrations](https://github.com/users/eps1lon/projects/3/views/9). Codemod implementation is up to the community which is tracked in the [list of missing React 19 codemods](https://github.com/eps1lon/types-react-codemod/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc+label%3A%22React+19%22+label%3Aenhancement).

- `ClassicComponentClass`: Use `ClassicComponentClass` from `create-react-class` instead
- `ClassicComponent`: Use `ClassicComponent` from `create-react-class` instead.
- `ClassicElement<Props>`: Use `ClassicElement<Props, InstanceType<T>>` from `create-react-class` instead.
- `ComponentSpec`: Use `ComponentSpec` from the `create-react-class` package instead.
- `Mixin`: Use `Mixin` from the `create-react-class` package instead.
- `ReactChildren`: Use `typeof React.Children` instead.
- `ReactHTML`: Either use `ReactHTML` from  `react-dom-factories` or, if you used `keyof ReactHTML`, use `HTMLElementType` instead.
- `ReactSVG`: Either use `ReactSVG` from  `react-dom-factories` or, if you used `keyof ReactSVG`, use `SVGElementType` instead.
- `SFCFactory` (no replacement)

### Changes to Type parameters

#### `useReducer`

`useReducer` now has improved type inference thanks to @mfp22.
However, this required a breaking change where we don't accept the full reducer type as a type parameter but instead either want none (and rely on contextual typing) or want both the state and action type.

We encourage everybody to stop relying on type parameters first e.g.
```diff
-useReducer<React.Reducer<State, Action>>(reducer)
+useReducer(reducer)
```

However, this may not work in edge cases where you can explicitly type the state and action instead via
```diff
-useReducer<React.Reducer<State, Action>>(reducer)
+useReducer<State, [Action]>(reducer)
```

If you define the reducer inline, we encourage to explicitly type the parameters instead which you'd also have to do if you move the reducer outside of the `useReducer` call:
```diff
-useReducer<React.Reducer<State, Action>>((state, action) => state)
+useReducer((state: State, action: Action) => state)
```

#### `ReactElement`

The `props` of React elements now default to `unknown` instead of `any` if the element is typed as `ReactElement`. Where you explicitly typed a value as `ReactElement` you can codemod the old behavior with `react-element-default-any-props`. The codemod only exists if you have a lot of legacy code relying on unsound access of element props. Element introspection only exists as an escape hatch and you should make it explicit that your props access is unsound.

### Component types

Due to the removal of legacy context, forward ref render functions (e.g. `(props: P, ref: Ref<T>) => ReactNode` will now be rejected by TypeScript if used as a component type.

This was almost always a bug that needed fixing by wrapping the render function in `forwardRef` or removing the second `ref` parameter.

### Ref cleanup

Since React now allows returning a "cleanup function" from callback refs, returning anything else will now be rejected by TypeScript. Mostly because a structural type system can't decide if you wanted to return a cleanup function and returned something else instead or didn't want to return a cleanup function and tell React to ignore the returned value.

The fix is usually to stop using implicit returns e.g.

```diff
-<div ref={current => (instance = current)} />
+<div ref={current => {instance = current}} />
```

The original code returned the instance of the `HTMLDivElement` and TypeScript wouldn't know if this was supposed to be a cleanup function or if you didn't want to return a cleanup function.
In the new code it is explicit that you didn't want to return a cleanup function.

You can codemod this pattern with [`no-implicit-ref-callback-return
`](https://github.com/eps1lon/types-react-codemod/#no-implicit-ref-callback-return)

### `propTypes` and `defaultProps` statics

`propTypes` are now ignored by React. However, to ease migration, we just type `propTypes` as `any` to ease migration in case these components are a bridge between typed and untyped components. If we'd remove `propTypes` entirely, a lot of assignments would cause TypeScript issues.

The same does not apply to `defaultProps` on function components since not rejecting them during type-checking would cause actual issues at runtime. Please check out the changelog entry for the removal of `defaultProps` to learn how to migrate off of `defaultProps`.


### Ref changes

`useRef` now requires an argument. This matches `createContext`:
```ts
// @ts-expect-error: Expected 1 argument but saw none
useRef();
// Passes
useRef(undefined);
// @ts-expect-error: Expected 1 argument but saw none
createContext();
// Passes
createContext(undefined);
```

Check out [[RFC] Make all refs mutable](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/64772) for prior discussions about this change.

When you apply the [`useRef-required-initial` codemod](https://github.com/eps1lon/types-react-codemod#useref-required-initial) (part of `preset-19`), all `useRef()` calls will be converted to `useRef(undefined)`.

We no longer try to guess whether a ref is fully managed by React or user code. `MutableRef` is now deprecated in favor of a single `RefObject` type which `useRef` will always return:

```ts
interface RefObject<T> {
  current: T
}

declare function useRef<T>: RefObject<T>
```

We still have a convenience overload for `useRef<T>(null)` that automatically returns `RefObject<T | null>`.
To ease migration due to the required argument for `useRef`, we also added a convenience overload for `useRef(undefined)` that automatically returns `RefObject<T | undefined>`.

## Migrating

Almost all of the `@types/*` packages are already compatible with React 19 types unless they specifically rely on React 18. Therefore it is advised to upgrade all `@types/*` packages first (at least the ones related to React e.g. packages starting with `react`).
Next, you should apply the [`preset-19` codemod from `types-react-codemod`](https://github.com/eps1lon/types-react-codemod/#preset-19) in its default configuration via `npx types-react-codemod@latest preset-19 ./path-to-your-react-ts-files`. In our experience, this covered most breaking changes.

For concrete examples of migrations with regards to React types, checkout the [example migrations done on libraries e.g. MUI or apps e.g. Bluesky](https://github.com/users/eps1lon/projects/3/views/9).

The largest block of remaining type issues relate to props of React elements now defaulting to `unknown` instead of `any`. If you're focus is on migration instead of soundness, you can use the [`react-element-default-any-props`](https://github.com/eps1lon/types-react-codemod/#react-element-default-any-props) to resolve a large portion of the breaking changes related to `ReactElement`. However, the codemod can't cover every pattern. You probably have to manually adjust the lines relying on `any` in `element.props` either by additional runtime checking or manual casts to `any`. You can check out the [example migrations done on libraries e.g. MUI or apps e.g. Bluesky](https://github.com/users/eps1lon/projects/3/views/9)  to get an idea of the possible patterns.
