## Angular CDK Coercion: The missing API reference

*Let's explore the utilities under `@angular/cdk/coercion` namespace*

Angular is a feature-rich framework that has been globally adopted for small-scale to enterprise-scale web applications. One of the most obvious reasons is the tremendous amount of APIs and utilities available out of the box to help in everyday development. The core team at Angular has done an amazing job in maintaining & thoroughly documenting everything along with the community contributors, but obviously, there are some patches that are contributed by community and still pending to be documented. In this article, we will explore one of those interesting and quite helpful set of utilities, i.e. *Coercion*.

#### What is Coercion?
*Coercion is the process of implicitly converting data to a required format.*

There are no hard and fast rules for coercion or implicit type conversion, and may vary from case to case. But our friends at ** [Angular](https://angular.io/) ** and the amazing community got us covered for some basic data types like arrays, booleans, numbers, etc. They’ve built a collection of APIs that can be found under `@angular/cdk/coercion` namespace.

We'll take a detailed look at these utilities in the following sections. In all the examples, we’ll pass data as `@Input` properties of our example components and try to coerce the passed values through the coercion APIs, but these APIs can be used anywhere based on your needs.

#### *Important:*
*Angular Ivy comes with stricter type checking that can be optionally disabled by using the TypeScript config (`tsconfig.json`) flags. We’ll see code examples that work with strict type checking enabled as well. You can read more about `Angular’s Template type checking`  [here](https://angular.io/guide/template-typecheck) .*
***
## coerceArray

> export function coerceArray(value: T | T[]): T[]
>
> Wraps the provided value in an array, unless it is an array already

Consider an example of a component that takes an array of strings and an array of a custom type `Person` as `@Input`.


```
// array-coercion.component.ts

import { Component, Input } from '@angular/core';
import { Person } from './person.interface';

@Component({
  selector: 'array-coercion',
  template: `...`
})
export class ArrayCoercionComponent  {
  @Input() strings: Array<string>;
  @Input() persons: Array<Person>;
}
```

```
// person.interface.ts

export interface Person {
  name: string;
  age: number;
}
```

This is how typically it will be used:

```
<!-- app.component.html  -->

<!-- person1 & person2 are objects of type Person  -->
<array-coercion
  [strings]="['array item 1', 'array item 2']"
  [persons]="[person1, person2]"
></array-coercion>
```

It will be nice if we allow passing in a single value and make it work without errors, like this:

```
<!-- app.component.html  -->

<!-- person1 is an object of type Person  -->
<array-coercion
  [strings]="'array item 1'"
  [persons]="person1"
></array-coercion>
```

To get this working, we will have to make the following changes to the `ArrayCoercionComponent`.

```
// array-coercion.component.ts

import { Component, Input } from '@angular/core';
import { coerceArray } from '@angular/cdk/coercion'; // NEW IMPORT
import { Person } from './person.interface';

@Component({
  selector: 'array-coercion',
  template: `...`
})
export class ArrayCoercionComponent  {
  // In the following code, `@Input strings` will NOT work with strict type checking
  // But we'll make the `@Input persons` to work with strict type checking as well
  
  // Declare private properties to hold coerced arrays
  private _stringArray: Array<string>;
  private _personArray: Array<Person>;
  
  // We have to separate this getter and name it differently to be used in the template
  // This works in combination with the `@Input set persons` defined on line 36
  get coercedPersons(): Array<Person> {
    return this._personArray;
  }

  // Use setter to call coerceArray method and convert passed values to arrays
  @Input()
  get strings(): Array<string> {
    return this._stringArray;
  }
  set strings(val: Array<string>) {
    this._stringArray = coerceArray(val);
  }

  // Note that the val parameter excepts a non-array value as well
  // We have to do this for strict type checking to work properly
  // If we don't do this, following error will be thrown:
  // Type 'Person' is not assignable to type 'Person[]'.
  @Input()
  set persons(val: Person | Array<Person>) {
    this._personArray = coerceArray(val);
  }
}
```

*Q: What happens if the passed in value is `null` or `undefined`?*

 *A: It gets wrapped into an array as well, see console log below:*


![1_3FDpCWkQjSSSRzXdAN3eCA.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1598990215157/EvyUHuwnY.png)

You can see, we still get an array but with a single item being `null` or `undefined`. I believe this API can be improved in the following ways:

1. If the value is `null` or `undefined`, it should not be wrapped in an array, so the consumers can do a simple *truthy/falsy* check.
2. Remove all `null` or `undefined` items from the array, so to make it clean to be bound without additional *truthy/falsy* checks on array items.

As we have seen in the above examples, using the `coerceArray` method, we allow the users of our component to pass in an array or a single value without producing any error.
***
## coerceBooleanProperty

> function coerceBooleanProperty(value: any): boolean
>
> Converts a value (typically a string) to a boolean

Let’s take an example of a component that takes two boolean flags as `@Input`.

```
// boolean-coercion.component.ts

import { Component, Input } from '@angular/core';

@Component({
  selector: 'boolean-coercion',
  template: `...`
})
export class BooleanCoercionComponent  {
  @Input() flagOne: boolean;
  @Input() flagTwo: boolean;
}
```

Example usage of this component will be like the following examples:

```
<!-- app.component.html -->

<!-- Bound with class properties -->
<boolean-coercion
  [flagOne]="booleanTrue"
  [flagTwo]="booleanFalse"
>
</boolean-coercion>

<!-- Bound with strings expressions -->
<boolean-coercion
  [flagOne]="'true'"
  [flagTwo]="'false'"
>
</boolean-coercion>
```

Although these examples show the common way of usage, but as these are booleans, it will be great if we can allow using them like these as well:

```
<!-- app.component.html -->

<!-- Input names only, Angular will set the value as empty string ('') -->
<!-- Supposed to be truthy -->
<boolean-coercion flagOne flagTwo></boolean-coercion>

<!-- Strings true/false, without expressions -->
<boolean-coercion flagOne="false" flagTwo="true"></boolean-coercion>

<!-- Random string values, without expressions -->
<boolean-coercion flagOne="random" flagTwo="random"></boolean-coercion>

<!-- Number, with or without expressions -->
<boolean-coercion [flagOne]="0" flagTwo="1"></boolean-coercion>

<!-- Some object, null or undefined value -->
<boolean-coercion [flagOne]="person1" [flagTwo]="nullValue"></boolean-coercion>
```

Let’s make some changes to our `BooleanCoercionComponent` to make it work with the above usage examples as well.

```
// boolean-coercion.component.ts

import { Component, Input } from '@angular/core';
import { coerceBooleanProperty } from '@angular/cdk/coercion'; // NEW IMPORT

@Component({
  selector: 'boolean-coercion',
  template: `...`
})
export class BooleanCoercionComponent  {
  // In the following code, `@Input flagOne` will NOT work with strict type checking
  // But we'll make the `@Input flagTwo` to work with strict type checking as well
  
  // Declare private properties to hold coerced booleans
  private _flagOne: boolean;
  private _flagTwo: boolean;

  // We have to separate this getter and name it differently to be used in the template
  // This works in combination with the `@Input set flagTwo` defined on line 36
  get coercedFlagTwo(): boolean {
    return this._flagTwo;
  }

  // Use setter to call coerceBooleanProperty method and convert passed values to boolean
  @Input()
  get flagOne(): boolean {
    return this._flagOne;
  }
  set flagOne(val: boolean) {
    this._flagOne = coerceBooleanProperty(val);
  }

  // Note that the val parameter excepts value of type 'any'
  // We have to do this for strict type checking to work properly
  // If we don't do this, following (or similar) error will be thrown:
  // Type 'string' is not assignable to type 'boolean'.
  @Input()
  set flagTwo(val: any) {
    this._flagTwo = coerceBooleanProperty(val);
  }
}
```

_**Note:** In the strict type checking scenario, we are allowing `any` value to be passed, the catch here is that if an `object` is passed as input, it will also be coerced to a boolean. That’s where the component users will have to be careful while passing the inputs to avoid unwanted results._

The `coerceBooleanProperty` utility method is super handy to allow your component users with multiple ways to pass boolean inputs.
***
## coerceCssPixelValue

> function coerceCssPixelValue(value: any): string
>
> Coerces a value to a CSS pixel value

This might be rare, but if you are in a situation where you have a need to pass a CSS pixel value to a component, which might use that value for some inline styling or some other logic, you will have something like this:

```
// css-pixel-coercion.component.ts

import { Component, Input } from '@angular/core';

@Component({
  selector: 'css-pixel-coercion',
  templateUrl: './css-pixel-coercion.component.html'
})
export class CssPixelCoercionComponent  {
  @Input() padding: string;
}
```

```
<!-- css-pixel-coercion.component.html -->

<!-- Using Angular's Style Binding expression -->
<!-- The value of 'padding' will have 'px' appended to it -->
<!-- But it will be limited only to 'px' and won't allow 'em', 'rem' or '%' -->
<!-- Similarly if we do [style.padding.em], it will be limited to 'em' only -->
<div class="default" [style.padding.px]="padding">
  Hello Coercion, this div has {{ padding }} padding!
</div>

<!-- Using Angular's Style Binding expression -->
<!-- We don't use the unit expression, and leave it open for any value to be passed -->
<!-- We can do similar to above example using a custom conversion method -->
<!-- But again, we will limit to a certain unit, e.g. 'px' or 'em' -->
<div class="default" [style.padding]="padding">
  Hello Coercion, this div has {{ padding }} padding!
</div>
```

The example usage of the above component will be the following:

```
<!-- app.component.html -->

<!-- This will work if we have [style.padding.px] -->
<css-pixel-coercion [padding]="10"></css-pixel-coercion>

<!-- This will work if we have [style.padding] -->
<css-pixel-coercion [padding]="'10px'"></css-pixel-coercion>
```

It will be more intuitive if we allow the users of our component to pass in number, pixel value, em, or a value with any other valid unit. Examples of such usage can be like this:

```
<!-- app.component.html -->

<!-- This gets converted as 10px -->
<css-pixel-coercion [padding]="10"></css-pixel-coercion>

<!-- The following strings get bound as-is -->
<!-- The component users can pass in values with other units as well -->
<css-pixel-coercion [padding]="'10px'"></css-pixel-coercion>
<css-pixel-coercion [padding]="'1em'"></css-pixel-coercion>
<css-pixel-coercion [padding]="'1rem'"></css-pixel-coercion>
```

To make this work, let’s see how our `CssPixelCoercionComponent` will look like.

```
// css-pixel-coercion.component.ts

import { Component, Input } from '@angular/core';
import { coerceCssPixelValue } from '@angular/cdk/coercion'; // NEW IMPORT

@Component({
  selector: 'css-pixel-coercion',
  template: `...`
})
export class CssPixelCoercionComponent  {
  // In the following code, `@Input paddingX` will NOT work with strict type checking
  // But we'll make the `@Input paddingY` to work with strict type checking as well
  
  // Declare private properties to hold coerced booleans
  private _paddingX: string;
  private _paddingY: string;

  // We have to separate this getter and name it differently to be used in the template
  // This works in combination with the `@Input set paddingY` defined on line 36
  get coercedPaddingY(): string {
    return this._paddingY;
  }

  // Use setter to call coerceCssPixelValue method and convert passed values to pixel string
  @Input()
  get paddingX(): string {
    return this._paddingX;
  }
  set paddingX(val: string) {
    this._paddingX = coerceCssPixelValue(val);
  }

  // Note that the val parameter excepts value of type 'number | string'
  // We have to do this for strict type checking to work properly
  // If we don't do this, following error will be thrown:
  // Type 'number' is not assignable to type 'string'.
  @Input()
  set paddingY(val: number | string) { // [A] More details below
    this._paddingY = coerceCssPixelValue(val);
  }
}
```

`[A]`: As we saw that the `coerceCssPixelValue` method takes the value parameter of type `any` which means we could’ve written the setter as:

`set paddingY(val: any)`

But we restricted it only to pass in `number` or `string` only. The reason was that, if the user passes `null` or `undefined`, it would be converted to empty strings and if an `object` gets passed, it would be stringified as `[object Object]px`. So, we restricted the input but kept the flexibility to pass valid, usable values.
***
## coerceElement

> function coerceElement(elementOrRef: ElementRef | T): T
>
> Coerces an ElementRef into an Element, unless it is already an Element

At times, we might need a component to accept a native HTML element as an input of our component and add some functionality around it. An example component might look something like this:

```
// element-coercion.component.ts

import { Component, Input } from '@angular/core';

@Component({
  selector: 'element-coercion',
  template: `...`
})
export class ElementCoercionComponent  {
  @Input() elementOne: Element;
  @Input() elementTwo: Element;
}
```

Working with Angular, we make use of `ElementRef` wrapper very frequently, and we might want to allow passing in `ElementRef` to our component. So we can change the input as:

```
// element-coercion.component.ts

import { Component, ElementRef, Input } from '@angular/core';

@Component({
  selector: 'element-coercion',
  template: `...`
})
export class ElementCoercionComponent  {
  @Input() elementOne: Element | ElementRef;
  @Input() elementTwo: Element | ElementRef;
}
```

Now, we can pass in native `HTMLElement` or `ElementRef`, as in the following example.

```
<!-- app.component.html -->

<!-- Assuming that 'htmlElement' is a class property that returns a native HTML element -->
<!-- Assuming that 'elementRef' is a class property that returns an instance of ElementRef -->
<element-coercion
  [elementOne]="htmlElement"
  [elementTwo]="elementRef"
></element-coercion>
```

So far so good! But, in our `ElementCoercionComponent`, we needed only native HTML element. This is where we can make use of the `coerceElement` utility function, and change our component to look like this:

```
// element-coercion.component.ts

import { Component, ElementRef, Input } from '@angular/core';
import { coerceElement } from '@angular/cdk/coercion'; // NEW IMPORT

@Component({
  selector: 'element-coercion',
  template: ``
})
export class ElementCoercionComponent  {
  // In the following code, `@Input elementOne` will NOT work with strict type checking
  // But we'll make the `@Input elementTwo` to work with strict type checking as well
  
  // Declare private properties to hold coerced elements
  private _elementOne: Element;
  private _elementTwo: Element;

  // We have to separate this getter and name it differently to be used in the template
  // This works in combination with the `@Input set elementTwo` defined on line 36
  get coercedElementTwo(): Element {
    return this._elementTwo;
  }

  // Use setter to call coerceElement method and convert passed value to Element
  @Input()
  get elementOne(): Element {
    return this._elementOne;
  }
  set elementOne(val: Element) {
    this._elementOne = coerceElement(val);
  }

  // Note that the val parameter excepts value of type 'Element | ElementRef'
  // We have to do this for strict type checking to work properly
  @Input()
  set elementTwo(val: Element | ElementRef) {
    this._elementTwo = coerceElement(val);
  }
}
```

Sweet, right?
***
## coerceNumberProperty

> function coerceNumberProperty(value: any, fallbackValue = 0): number
>
> Coerces a data-bound value (typically a string) to a number

Passing numbers as component inputs or function arguments is another very common practice. Let’s suppose we have a component that takes two numbers as input.

```
// number-coercion.component.ts

import { Component, Input } from '@angular/core';

@Component({
  selector: 'number-coercion',
  template: `...`
})
export class NumberCoercionComponent  {
  @Input() numberOne: number;
  @Input() numberTwo: number;
}
```

Example usage of this component can be something like this:

```
<!-- app.component.html -->

<!-- Expression with a number value or a class property -->
<number-coercion [numberOne]="10" [numberTwo]="numericValue"></number-coercion>
```

It would be great if we can allow the following usage as well:

```
<!-- app.component.html -->

<!-- Inline string value or an expression with a string value -->
<number-coercion numberOne="10" [numberTwo]="stringValue"></number-coercion>
```

The input value can also be `null` or `undefined` and could cause unwanted situations or runtime errors. Let’s make some changes to our `NumberCoercionComponent` to make sure that the input values are always valid numbers after we receive them.

```
// number-coercion.component.ts

import { Component, Input } from '@angular/core';
import { coerceNumberProperty } from '@angular/cdk/coercion'; // NEW IMPORT

@Component({
  selector: 'number-coercion',
  template: `...`
})
export class NumberCoercionComponent  {
  // In the following code, `@Input numberOne` will NOT work with strict type checking
  // But we'll make the `@Input numberTwo` to work with strict type checking as well
  
  // Declare private properties to hold coerced numbers
  private _numberOne: number;
  private _numberTwo: number;

  // We have to separate this getter and name it differently to be used in the template
  // This works in combination with the `@Input set numberTwo` defined on line 36
  get coercedNumberTwo(): number {
    return this._numberTwo;
  }

  // Use setter to call coerceNumberProperty method and convert passed value to number
  @Input()
  get numberOne(): number {
    return this._numberOne;
  }
  set numberOne(val: number) {
    this._numberOne = coerceNumberProperty(val);
  }

  // Note that the val parameter excepts value of type 'any'
  // We have to do this for strict type checking to work properly
  // 'coerceNumberProperty' method takes a second parameter
  // The second parameter is the fallback or default value
  @Input()
  set numberTwo(val: any) {
    this._numberTwo = coerceNumberProperty(val, 12);
  }
}
```

There you go!
***
## Conclusion

In the sections above, we saw how these small utilities help us make sure that our component inputs receive the data gracefully with some level of flexibility.

Although this is a small list of utilities, I am sure we can make improvements to the existing ones and add more to this list for the amazing community. I would recommend you to take a look at the implementation of these APIs  [here](https://github.com/angular/components/tree/master/src/cdk/coercion) .

Looking forward to hearing your thoughts and feedback. Happy coding!