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