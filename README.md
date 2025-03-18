# CmpDeepDive

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 18.0.0-next.2.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The application will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.

<br>
<br>
<hr>

## When to split up components

Up to you!

1. Separation of Concerns (Every component should only do "one thing")
2. Simplicity and Code Colocation

## Extend built-in component

Your component elements do end up in the real DOM!

Your component elements are not compiled away or replaced with the template content by Angular, instead they are rendered to the DOM and the template content is then nested inside of them. It means we sometimes we have unnecessary element in the DOM (duplication).

```
<button>
<span>
    Logout
</span>
<span class="icon">
    →
</span>
</button>
```

```
@Component({
  selector: 'app-button',
  standalone: true,
  imports: [],
  templateUrl: './button.component.html',
  styleUrl: './button.component.css',
})
```

```
<li>
    <app-button />
</li>
```

<br>

So we can use attribute selector instead of element selector to remove duplication in the DOM

```
<span>
    Logout
</span>
<span class="icon">
    →
</span>
```

```
@Component({
  selector: 'button[appButton]',
  standalone: true,
  imports: [],
  templateUrl: './button.component.html',
  styleUrl: './button.component.css',
})
```

```
<li>
    <button appButton></button>
</li>
```

## Supporting Content Projection with Multiple Slots and more ng-content features

Instead of using @Inputs for each markup place, we can just use ng-content

```
<span>
    <ng-content />
</span>
<span class="icon">
    <ng-content />
</span>
```

`<button appButton>Logout →</button>`

But its not right, because everything will be rendered in second span. Angular does not which content should go where, so we have to tell him by adding select attribute. Selector wants a css selector

```
<span>
    <ng-content />
</span>

<ng-content select=".icon" />
```

and then add span icon into templates

```
<button appButton>Logout
    <span class="icon">→</span>
</button>
```

<hr>

If we do not want to have a span or other elements outside of component, we can move it to our inside component template

```
<span>
    <ng-content />
</span>

<span class="icon">
    <ng-content select=".icon" />
</span>
```

```
<button appButton>
    Logout
    <span class="icon">→</span>
</button>
```

But then, we will have a duplication in DOM again. To remove duplication we can use ngProjectAs attribute

`<span ngProjectAs="icon">→</span>`

```
<span class="icon">
    <ng-content select="icon" />
</span>
```

<hr>

You can use fallbacks in ng-content

`<ng-content select="icon">→</ng-content>`

In that case if no icon provided, it will use fallback icon → instead

<hr>

If we want restrict what can be insterted in ng-content, we can use select. You can specify multiple selectors separated by comma

```
<p>
    <label>{{ label }}</label>
    <ng-content select="input, textarea" />
</p>
```

## Encapsulation

None

- Disables style scoping

The Shadow DOM

- A browser feature that allows you to attach hidden DOM structures to DOM elements.
- For CSS styling, the Shadow DOM can be used to scope CSS styles to that hidden tree - instead of applying styles globally to the entire page
- Angular can emulate this Shadow DOM broswer feature for its own components

Emulated (default)

```
enum ViewEncapsulation {
    Emulated = 0,
    None = 2,
    ShadowDom = 3
}
```

Internally, the pre-defined allowed values map to integers which can be used by Angular as identifiers

```
@Component({
  selector: 'app-control',
  standalone: true,
  imports: [],
  templateUrl: './control.component.html',
  styleUrl: './control.component.css',
  encapsulation: ViewEncapsulation.None,
  host: {
    class: 'control'
  }
})
```

<br>

## Host Elements

Every Angular component has a Host Element

Example

A component with a selector of "app-header" targets an `<app-header>` element which is rendered into the real DOM

IMPORTANT: The elements targeted by your component selectors do NOT act as placeholders and are NOT replaced when the page is rendered!

Instead, the selected elements are preserved and simply "enhanced" / taken over by your component logic and markup!

- To target host element we use :host selector (Does allow to directly apply styles to rendered host element)

- The component host element is NOT considered a part of the component template but will be affected by the (scoped) component styles via :host

<hr>

**We can get rid of CSS scoping with Host Element**

**_- If we have a component which is not using CSS styles directly in component, instead its getting read from outside, we can set host to the component and use :host in CSS to ignore encapsulation_**

Instead of .dashboard-item, we use :host

```
:host {
  display: block;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 6px;
  background-color: #f8f8f8;
  box-shadow: 0 1px 6px 0 rgba(0, 0, 0, 0.2);
}

:host header {
  display: flex;
  padding: 0;
  gap: 0.75rem;
  align-items: center;
  margin-bottom: 1rem;
}
```

<br>

```
@Component({
  selector: 'app-dashboard-item',
  standalone: true,
  imports: [],
  templateUrl: './dashboard-item.component.html',
  styleUrl: './dashboard-item.component.css',

})
```

<hr>

Alternative way to set host class **HostListener** and **HostBinding**

New and preferable (in decorator):

```
@Component({
  selector: 'app-control',
  standalone: true,
  imports: [],
  templateUrl: './control.component.html',
  styleUrl: './control.component.css',
  encapsulation: ViewEncapsulation.None,
  host: {
    class: 'control'
  }
})
```

Old and not preferable (in component class):

```
export class ControlComponent {
  @HostBinding('control') className = 'control';
}
```

Where:

1. ('control') is the actual property (if you want a different name for class)
2. className is property name
3. 'control' value for property

<hr>

We can bind a method to an event to listen

New and preferable:

```
@Component({
  selector: 'app-control',
  standalone: true,
  imports: [],
  templateUrl: './control.component.html',
  styleUrl: './control.component.css',
  encapsulation: ViewEncapsulation.None,
  host: {
    class: 'control',
    '(click)': 'onClick()'
  }
})
export class ControlComponent {
  @HostBinding('control') className = 'control';

  onClick() {
    console.log('Clicked!');
  }
}

```

Old and not preferable:

```
  host: {
    class: 'control',

  }
})
export class ControlComponent {
  @HostBinding('control') className = 'control';
  @HostBinding('click') onClick() {
    console.log('Clicked!');
  }

}
```

<hr>

Programmatic access to host element

`private el = inject(ElementRef);`

<br>

## Class Binding

You can bind class property on element dinamically

Single class:

`<div [class.status]="currentStatus === 'offline'">`

Multiple classes:

```
<div [class.status]="{
    status: true,
    'status-online': currentStatus === 'online',
    'status-offline': currentStatus === 'offline',
    'status-unknown': currentStatus === 'unknown'
}">
```

<hr>

### Styles Binding

You can also bind styles dinamically

```
<div [class.status]="{
    status: true,
    'status-online': currentStatus === 'online',
    'status-offline': currentStatus === 'offline',
    'status-unknown': currentStatus === 'unknown'
}"
[style]="{
    'font-size': '64px' //or TS expression
}">
```

<br>

## Literal Types

Setting specific string values as types uses a TypeScript feature called "Literal Types". The idea is to only allow specific (string) values - instead of all strings.

`currentStatus: 'online' | 'offline' | 'unknown' = 'online';`

<br>

## Component Lifecycle

[Documentation](https://angular.dev/guide/components/lifecycle)
[ExecutionOrder](https://angular.dev/guide/components/lifecycle#execution-order)

Use ngOnInit method instead of constructor

Keep constructor lean and only do basic class init work (Good practice)

You should prefer using ngOnInit for initialization work like setting up interval

```
ngOnInit() {
    setInterval(() => {
      const rnd = Math.random();
      if (rnd < 0.5) {
        this.currentStatus = 'online';
      } else if (rnd < 0.9) {
        this.currentStatus = 'offline';
      } else {
        this.currentStatus = 'unknown';
      }
    }, 5000);
  }
```

More complex tasks like sending http requests should not go to the constructor, but into ngOnInit

Difference:
In ngOnInit Angular is done initializing component inputs, so if component receives any input values, those values will be initialized and availabe in ngOnInit

<hr>

We have to get rid of typos errors, as its does not handle typos error in consoles or IDE

To avoid hard to debug problems its recommended to use specific TypeScript feature that is embraced by Angular by Lifecycle methods

We force ourself to use OnInit interface methods

```
export class ServerStatusComponent implements OnInit{
  currentStatus: 'online' | 'offline' | 'unknown' = 'online';

  ngOnInit() {
    setInterval(() => {
      const rnd = Math.random();
      if (rnd < 0.5) {
        this.currentStatus = 'online';
      } else if (rnd < 0.9) {
        this.currentStatus = 'offline';
      } else {
        this.currentStatus = 'unknown';
      }
    }, 5000);
  }
}
```

