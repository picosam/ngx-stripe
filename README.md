# ngx-stripe

[![version](https://img.shields.io/npm/v/ngx-stripe.svg)](https://www.npmjs.com/package/ngx-stripe)
[![license](https://img.shields.io/npm/l/express.svg)](https://www.npmjs.com/package/ngx-stripe)

Angular 2+ wrapper for StripeJS

## Features

* Stripe Service
* Lazy script loading

## Installation

To install this library, run:

```bash
$ npm install ngx-stripe --save
```

## Using the library

Import the `NgxStripeModule` into the application

The module takes the same parameters as the global Stripe object. The APIKey and the optional options to use Stripe connect

* apiKey: string
* options: {
  stripeAccount?: string;
}

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

// Import your library
import { NgxStripeModule } from 'ngx-stripe';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    NgxStripeModule.forRoot({
      apiKey: '***your-stripe-publishable key***'
    }),
    LibraryModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Stripe Service

Once imported, you can inject the StripeService anywhere you need. The stripe script will be loaded the first time the service is injected.

The stripe service exposes the same methods as the StripeJS instance but with typescript types. The API is based on Observables so it can be combined with other actions.

In the example below, the component mounts the card in the [OnInit](https://angular.io/guide/lifecycle-hooks#oninit) lifecycle. The buy button creates a Stripe token the could be sent to the server for further actions. In this example we just log that token to the console:

Example component (more HTML and CSS examples can be found at the [Stripe Elements Examples](https://stripe.com/docs/elements/examples)):
```xml
<form novalidate (ngSubmit)="buy($event)" [formGroup]="stripeTest">
  <input type="text" formControlName="name" placeholder="Jane Doe">
  <div id="card-element" class="field" #card></div>
  <button type="submit">
    BUY
  </button>
</form>
```
```typescript
import { Component, OnInit, ViewChild, ElementRef } from '@angular/core';
import { FormGroup, FormBuilder, Validators } from "@angular/forms";

import { StripeService, Elements, Element as StripeElement } from "ngx-stripe";

@Component({
  selector: 'app-stripe-test',
  templateUrl: 'stripe.html'
})
export class StripeTestComponent implements OnInit {
  elements: Elements;
  card: StripeElement;
  @ViewChild('card') cardRef: ElementRef;

  stripeTest: FormGroup;

  constructor(
    private fb: FormBuilder,
    private stripeService: StripeService) {}

  ngOnInit() {
    this.stripeTest = this.fb.group({
      name: ['', [Validators.required]]
    });
    this.stripeService.elements()
      .subscribe(elements => {
        this.elements = elements;
        // Only mount the element the first time
        if (!this.card) {
          this.card = this.elements.create('card', {
            style: {
              base: {
                iconColor: '#666EE8',
                color: '#31325F',
                lineHeight: '40px',
                fontWeight: 300,
                fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
                fontSize: '18px',
                '::placeholder': {
                  color: '#CFD7E0'
                }
              }
            }
          });
          this.card.mount(this.cardRef.nativeElement);
        }
      });
  }

  buy() {
    const name = this.stripeTest.get('name').value;
    this.stripeService
      .createToken(this.card, { name })
      .subscribe(token => {
        if (result.token) {
          // Use the token to create a charge or a customer
          // https://stripe.com/docs/charges
          console.log(result.token);
        } else if (result.error) {
          // Error creating the token
          console.log(result.error.message);
        }
      });
  }
}
```

## Testing
The following command runs unit & integration tests that are in the `tests` folder, and unit tests that are in `src` folder: 
```Shell
npm test 
```

## Building
The following command:
```Shell
npm run build
```
- starts _TSLint_ with _Codelyzer_
- starts _AoT compilation_ using _ngc_ compiler
- creates `dist` folder with all the files of distribution

To test the npm package locally, use the following command:
```Shell
npm run pack-lib
```
You can then run the following to install it in an app to test it:
```Shell
npm install [path]to-library-[version].tgz
```

## Publishing

```Shell
npm run publish-lib
```

## Documentation
To generate the documentation, this starter uses [compodoc](https://github.com/compodoc/compodoc):
```Shell
npm run compodoc
npm run compodoc-serve 
```

## License

MIT © [Ricardo Sánchez Gregorio](mailto:me@richnologies.io)
