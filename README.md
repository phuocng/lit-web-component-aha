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

The event handlers of native elements works without indicating the full invocation:

```js
_handleSignIn(e: MouseEvent) {
  // ...
}

<button @click=${this._handleSignIn}>Sign in</button>
```

## Conditional rendering

If you don't want to render a certain part based on given condition, then remember to return an empty string (''), or `null`.

```js
// Does not work
// You will see `false` when inspecting element if the condition doesn't happen
// causing a blank space on the UI
${isLoggedIn && html`Only logged in users can see`}

// Work
${isLoggedIn ? html`Only logged in users can see` : ''}
```

## SVG slot

SVG elements don't support `slot`.

```js
@customElement('icon')
class Icon extends LitElement {
  // Does not work
  render() {
    return html`
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" height="16" width="16">
        <slot></slot>
      </svg>
    `;
  }
}
```
