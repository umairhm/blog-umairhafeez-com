## Quick Tip: Centralized Error Handling

> Graceful error handling is a key feature of good software.

Angular comes with a built-in mechanism for centralized error handling. It's really handy to deal with unhandled exceptions.

Here is how we can quickly get started.

We'll create a class that extends ErrorHandler that is part of `@angular/core` and implement the handleError method, that gives us the `error` object.

```
import { NgModule, ErrorHandler } from '@angular/core';

export class CustomErrorHandler implements ErrorHandler {
  handleError(error: any) {
    console.warn('Handled using custom handler: ', {
      name: error.name,
      message: error.message,
      stack: error.stack || ''
    });
  }
}

@NgModule({
  providers: [
    {
      provide: ErrorHandler,
      useClass: CustomErrorHandler
    }
  ]
})
export class AppModule { }

``` 

We can use any logging provider to log these errors and present the user with a friendly message to keep up with a good UX.

A working example can be found on this link:

https://stackblitz.com/edit/angular-ivy-resq5p?file=src/app/app.module.ts

I hope you like this tip!