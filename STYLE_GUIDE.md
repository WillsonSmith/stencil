# Stencil Style Guide

This is a component style guide created and enforced internally by the core team of Stencil, for the purpose of standardizing [Ionic Core](http://ionicframework.com/) components. This should only be used as a reference for other teams in creating their own style guides. Feel free to modify to your teams own preference.


## File structure

- Only one component per file.
- Implementation (.tsx) and styles of a component should live in the same folder.
- One component per folder unless components share the styles and are strongly coupled.

Example from ioni-core:

```
├── card
│   ├── card.ios.scss
│   ├── card.md.scss
│   ├── card.scss
│   ├── card.tsx
│   └── test
│       └── basic
│           ├── e2e.js
│           └── index.html
├── card-content
│   ├── card-content.ios.scss
│   ├── card-content.md.scss
│   ├── card-content.scss
│   └── card-content.tsx
├── card-title
│   ├── card-title.ios.scss
│   ├── card-title.md.scss
│   ├── card-title.scss
```

Or tabs, where all them live in the same folder:

```
├── tabs
│   ├── page-tab.tsx
│   ├── tab-bar.tsx
│   ├── tab-button.tsx
│   ├── tab-highlight.tsx
│   ├── tab.tsx
│   ├── tabs.ios.scss
│   ├── tabs.md.scss
│   ├── tabs.scss
│   ├── tabs.tsx
│   └── test
│       └── basic
│           ├── e2e.js
│           └── index.html
```


## Naming
### HTML tag

#### Prefix
The prefix has a mayor role when you are creating a collection of components intended to be used across diferent projects, like @ionic/core. Web Components are not scoped because they are globally declared within the webpage, which means an "unique" prefix is needed to prevent collisions. The prefix is also able help to quickly indentify the collection of an component. Additionally, web components are required to contain a "-" dash within the tag name, so using the part to namespace your components is a natural fit.

We don't recommend using "stencil" as prefix, since Stencil DOES NOT emit stencil components, but rather the output is simply standard compliant web components.

DO NOT do this:
```
stencil-component
stnl-component
```

Instead, use your own naming or brand.


#### Name

Components are not actions, they are conceptually "things". It is better to use nouns, instead of verbs, such us: "animation" instead of "animating". "input", "tab", "nav", "menu" are some examples.


#### Modifiers

When several components are related and/or coupled, it is a good idea to share the name, and then add different modifiers, for example:

```
ion-menu
ion-menu-controller
```

```
ion-card
ion-card-header
ion-card-content
ion-card-footer
```


### Component (TS class)

The name of the ES6 class of the components SHOULD NOT have prefix since classes are scoped. There is no risk of collision.

```ts
@Component({tag: 'ion-menu'})
export class Menu {}

@Component({tag: 'ion-menu-controller'})
export class MenuController {}

@Component({tag: 'page-home'})
export class PageHome {}
```


## TypeScript

1. **Follow** [tslint-ionic-rules](https://github.com/ionic-team/tslint-ionic-rules/blob/master/tslint.js)

2. **Variable decorators should be inlined.**

 ```ts
@Prop() name: string;
@Element() el: HTMLElement;
```

3. **Method decorator should be multi-line**

 ```ts
@Listen('click')
onClick() {
}
```

4. **Use private variables and methods as much possible:** They are useful to detect deadcode and enforce encapsulation. Note that this is a feature which TypeScript provides to help harden your code, but using `private`, `public` or `protected` does not make a difference in the actual JavaScript output.


## Code organization

### Newspaper Metaphor from The Robert C. Martin's Clean Code

> The source file should be organized like a newspaper article, with the highest level summary at the top, and more and more details further down. Functions called from the top function come directly below it, and so on down to the lowest level and most detailed functions at the bottom. This is a good way to organize the source code, even though IDE:s make the location of functions less important, since it is so easy to navigate in and out of them.

### High level example (commented)

```ts
@Component({
  tag: 'ion-something',
  styleUrl: 'something.scss',
  styleUrls: {
    ios: 'something.ios.scss',
    md: 'something.md.scss',
    wp: 'something.wp.scss'
  },
  host: {
    theme: 'something'
  }
})
export class Something {

  /**
   * 1. Own Properties
   * Always set a the type if a default value has not
   * been set. If a default value is being set, then type
   * is already inferred. List the own properties in
   * alphabetical order. Note that because these properties
   * do not have the @Prop() decorator, they will not be exposed
   * publicly on the host element, but only used internally.
   */
  num: number;
  someText = 'default';

  /**
   * 2. Reference to host HTML element.
   * Inlined decorator
   */
  @Element() el: HTMLElement;

  /**
   * 3. State() variables
   * Inlined decorator, alphabetical order.
   */
  @State() isValidated: boolean;
  @State() status = 0;

  /**
   * 4. Internal props (context and connect)
   * Inlined decorator, alphabetical order.
   */
  @Prop({ context: 'config' }) config: Config;
  @Prop({ connect: 'ion-menu-controller' }) lazyMenuCtrl: Lazy<MenuController>;

  /**
   * 5. Public Property API
   * Inlined decorator, alphabetical order. These are
   * different than "own properties" in that public props
   * are exposed as properties and attributes on the host element.
   */
  @Prop() content: string;
  @Prop() enabled: boolean;
  @Prop() menuId: string;
  @Prop() type = 'overlay';

  /**
   * NOTE: Prop lifecycle events SHOULD go just behind the Prop they listen to.
   * This makes sense since both statements are strongly connected.
   * - Refacting the instance variable name, must also update the name in @PropDidChange()
   * - Code is easier to follow and maintain.
   */
  @Prop() swipeEnabled = true;
  @PropDidChange('swipeEnabled')
  swipeEnabledChange() {
    this.updateState();
  }

  /**
   * 6. Events section
   * Inlined decorator, alphabetical order.
   */
  @Event() ionClose: EventEmitter;
  @Event() ionDrag: EventEmitter;
  @Event() ionOpen: EventEmitter;

  /**
   * 7. Component lifecycle events
   * Ordered by their natural call order, for example
   * WillLoad should go before DidLoad.
   */
  componentWillLoad() {}
  componentDidLoad() {}
  componentDidUnload() {}

  /**
   * 8. Listeners
   * It is ok to place them in a different location
   * if makes more sense in the context. Recommend
   * starting a listener method with "on".
   *
   * Always use two lines.
   */
  @Listen('click', { enabled: false })
  onClick(ev: UIEvent) {
    console.log('hi!')
  }

  /**
   * 9. Public methods API
   * These methods are exposed on the host element.
   * Always use two lines.
   */
  @Method()
  open() {
    ...
  }

  @Method()
  close() {
    ...
  }

  /**
   * 10. Local methods
   * Internal business logic. These methods cannot be
   * called from the host element.
   */
  prepareAnimation(): Promise<void> {
    ...
  }

  updateState() {
    ...
  }

  /**
   * 11. hostData() function
   * Used to dynamically set host element attributes.
   * Should be placed directly above render()
   */
  hostData() {
    return {
      attribute: 'navigation',
      side: this.isRightSide ? 'right' : 'left',
      type: this.type,
      class: {
        'something-is-animating': this.isAnimating
      }
    };
  }

  /**
   * 12. render() function
   * Always the last one in the class.
   */
  render() {
    return (
      <div class='menu-inner page-inner'>
        <slot></slot>
      </div>
    );
  }
}
```