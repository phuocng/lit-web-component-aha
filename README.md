# Lit library aha

I recently migrate some projects made in React and Svelte to web components. Here are some of aha moments when porting them to the [Lit library](https://lit.dev).

## Callback property

If a component provides a callback as a property:

```js
@customElement('todo-list')
class TodoList extends LitElement {
  @property({ type: Function })
  onMarkAllCompleted: () => void = () => {};
}
```

then consumers have to provide the full invocation:

```js
_handleMarkAllCompleted() {
  // ...
}

// Does not work
<todo-list
  .onMarkAllCompleted=${this._handleMarkAllCompleted}
/>

// Work
<todo-list
  .onMarkAllCompleted=${() => this._handleMarkAllCompleted()}
/>
```
