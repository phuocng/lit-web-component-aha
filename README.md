# Lit library aha

I recently migrate some projects made in React and Svelte to web components. Here are some of aha moments when porting them to the [Lit library](https://lit.dev).

The list doesn't complete yet as I continue learning more about Lit. Feel free to include your experiences. I hope it helps you when migrating works from popular front-end libraries to Lit.

## Auto close tags

Always include close tags.

```js
// Does not work
<custom-spacer />
<div />

// Work
<custom-spacer></custom-spacer>
<div></div>
```

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

## Disabled attribute

`disabled="false"` isn't a valid attribute declaration. If you set it with a `button`, then the `click` event handler won't be triggered.

```js
_handleClickSignIn() {
  // ...
}

<button disabled="false" @click=${this._handleClickSignIn}>Sign in</button>
```

In most cases, the value of `disabled` attribute is determined by a given condition:

```js
// Does not work if the condition doesn't match
<button 
  disabled=${userNameEmpty || passwordEmpty}
  @click={...}
>
  ...
</button>
```

Instead, use the `ifDefined` directive:

```js
// Work
import { ifDefined } from 'lit/directives/if-defined.js';

<button
  ?disabled=${ifDefined(isButtonDisabled ? true : undefined)}
  @click={...}
>
  ...
</button>
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

## Use em instead of rem unit

The `rem` unit applies the root styles which are defined by the `html` element. The most popular issue that I encounted is the web component font size is small in comparison to the containing page:

```css
// The containing page
body {
  font-size: 1.5rem;
}
html {
  font-size: 62.5%;
}
```

Using the `em` unit fixes the issue. The unit should be used not only for font size, but also for spacing (`padding`, `margin`).

```css
// Do not
@customElement('kit-button')
export class KitButton extends LitElement {
  static styles = css`
    :host {
      font-size: 1rem;
    }
  }
}

// Do
@customElement('kit-button')
export class KitButton extends LitElement {
  static styles = css`
    :host {
      font-size: 1em;
    }
  }
}
```
