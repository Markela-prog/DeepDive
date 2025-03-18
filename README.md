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

Read Angular [Documentation](https://angular.dev/guide/components/lifecycle)

Component lifecycle [ExecutionOrder](https://angular.dev/guide/components/lifecycle#execution-order)

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

<hr>

In some cases, we have to make sure that if component will disappear, we clean up the interval whenever that component is removed. For example: if we have an interval that keeps on going behind the scenes, eventhough component is gone, you have a memory leak in application.

```
export class ServerStatusComponent implements OnInit, OnDestroy{
  currentStatus: 'online' | 'offline' | 'unknown' = 'online';
  private interval?: ReturnType<typeof setInterval>;

  ngOnInit() {
    this.interval = setInterval(() => {
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

  ngOnDestroy(): void {
    clearTimeout(this.interval);
  }
}
```

In that case we make sure that this interval gets cleaned up when componented is removed

<hr>

Modern alternative is to use destroyRef

```
export class ServerStatusComponent implements OnInit{
  currentStatus: 'online' | 'offline' | 'unknown' = 'online';
  private destroyRef = inject(DestroyRef);

  ngOnInit() {
    const interval = setInterval(() => {
      const rnd = Math.random();
      if (rnd < 0.5) {
        this.currentStatus = 'online';
      } else if (rnd < 0.9) {
        this.currentStatus = 'offline';
      } else {
        this.currentStatus = 'unknown';
      }
    }, 5000);

    this.destroyRef.onDestroy(() => {
      clearInterval(interval);
    })
  }
}
```

<br>

## Template variables

Angular feature where the element to which this variable (special attribute) is added is stored in variable, and this variable is available anywhere in the template

```
<form (ngSubmit)="onSubmit(titleInput)">
  <app-control label="Title">
    <input name="title" id="title" #titleInput />
  </app-control>
```

```
export class NewTicketComponent {
  onSubmit(titleElement: HTMLInputElement) {
    const enteredTitle = titleElement.value;
    console.log(enteredTitle);
  }
}
```

or alternative way

```
<form (ngSubmit)="onSubmit(titleInput.value, textInput.value)">
  <app-control label="Title">
    <input name="title" id="title" #titleInput />
  </app-control>
```

```
export class NewTicketComponent {
  onSubmit(title: string, ticketText: string) {
    console.log(title);
    console.log(ticketText);
  }
}
```

**_NOTE_**: If we use custom element attribute the type of template element will be not HTML element, but custom element. For example:

```
<button appButton #btn>
    Submit
    <span ngProjectAs="icon">⌲</span>
</button>
```

The #btn will be a reference of ButtonComponent

```
<button #btn>
    Submit
    <span ngProjectAs="icon">⌲</span>
</button>
```

And without appButton attribute it will be HTMLButtonElement

<hr>

### @ViewChild

To reset a form input fields, we can pass #form template element to submit function, and then use form.reset() function. But sometimes we cannot pass template elements into component class for some reasons, so there is a better way to do it

@ViewChild is a decorator that can be used to select elements in the template of component and make them available in component class.

ViewChild needs a selector as an argument (can be string (template variable name (!YOU CANT PASS CSS SELECTOR)) or class name of component e.g.(ButtonComponent, which will actually store instance of button component in property so you can interact with it))

`<form (ngSubmit)="onSubmit(titleInput.value, textInput.value)" #form>`

```
export class NewTicketComponent {

  @ViewChild('form') private form?: ElementRef<HTMLFormElement>;

  onSubmit(title: string, ticketText: string) {
    console.log(title);
    console.log(ticketText);
    this.form?.nativeElement.reset();
  }
}
```

Alternative using signal

```
export class NewTicketComponent {

  //@ViewChild('form') private form?: ElementRef<HTMLFormElement>;
  private form = viewChild.required<ElementRef<HTMLFormElement>>('form');

  onSubmit(title: string, ticketText: string) {
    console.log(title);
    console.log(ticketText);
    this.form().nativeElement.reset();
  }
}
```

## ContentChild

If we use ng-content, we cannot access inputs via @ViewChild, so we use @ContentChild

`<ng-content select="input, textarea" />`

```
<app-control label="Title">
        <input name="title" id="title" #titleInput #input/>
    </app-control>
    <app-control label="Request">
        <textarea name="request" id="request" rows="3" #textInput #input></textarea>
    </app-control>
```

***NOTE***: We use same template variable #input, but it creates a different instances of the inputs (1 for input and 1 for textarea)

```
export class ControlComponent {
  @Input({ required: true }) label!: string;
  private el = inject(ElementRef);
  @ContentChild('input') private control?: ElementRef<HTMLInputElement | HTMLTextAreaElement>;

  onClick() {
    console.log('Clicked!');
    console.log(this.el);
    console.log(this.control);
  }
}
```

We can also use signal there as in ViewChild

<br>

### ViewChildren/viewChildren and ContentChild/ContentChildren

Allows to select multiple elements e.g. Multiple custom buttons

<br>

### ngAfterViewInit/ngAfterContentInit

IF you want to use @ViewChild elements, you either use it in class methods or ngAfterViewInit, not in ngOnInit, because it will be undefined

```
private form = viewChild.required<ElementRef<HTMLFormElement>>('form');
  ngAfterViewInit() {
    console.log('AFter');
    console.log(this.form().nativeElement)
  }
```

<br>

### afterRender/afterNextREnder

Refering for entire application instead of component

<br>
<hr>

## More about loops

We can use @empty to specify a fallback code to be rendered if array is empty:

```
<div>
    <ul>
        @for (ticket of tickets; track ticket.id) {
            <li>
                <app-ticket />
            </li>
        } @empty {
            <p>No tickets available.</p>
        }
    </ul>
</div>
```

<br>

## Updating signal values

We can use update method to change signal value to the opposite by passing a function

```
export class TicketComponent {
  @Input({ required: true }) data?: Ticket;

  detailsVisible = signal(false);

  onToggleDetails() {
    //this.detailsVisible.set(!this.detailsVisible());
    this.detailsVisible.update((wasVisible) => !wasVisible);
  }
}
```