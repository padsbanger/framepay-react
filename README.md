# framepay-react

[![npm](https://img.shields.io/npm/v/@rebilly/framepay-react.svg)](https://www.npmjs.com/package/@rebilly/framepay-react)
[![Build Status](https://github.com/Rebilly/framepay-react/workflows/Build%20&%20Test/badge.svg)](https://github.com/Rebilly/framepay-react/actions)

> React components for FramePay.js

Supported: React 14, 15, 16 (latest)  

This package is a wrapper for [FramePay](https://rebilly.github.io/framepay-docs/) offering out-of-the-box support for Redux and other common React features.

## Table of Contents
- [FramePay documentation](#framepay-documentation)
- [Demos](#demos)
- [Installation](#installation)
- [Getting started](#getting-started)
    - [The FramePay context (`FramePayProvider`)](#the-framepay-context-framepayprovider)
    - [Setting up your payment form](#setting-up-your-payment-form)
        - [Provided props](#provided-props)
        - [With FramePay (`withFramePay`) HOC](#with-framepay-withframepay-hoc)
        - [Card elements (`withFramePayCardComponent`) HOC](#card-elements-withframepaycardcomponent-hoc)
        - [Bank elements (`withFramePayBankComponent`) HOC](#bank-elements-withframepaybankcomponent-hoc)
- [Advanced options](#advanced-options)
    - [Initialize settings](#initialization-settings)
    - [Create Token params](#create-token-parameters)
- [Troubleshooting](#troubleshooting)

### FramePay documentation
For more information on FramePay see its [official documentation](https://rebilly.github.io/framepay-docs/) or [repository](https://github.com/Rebilly/framepay-docs).

### Demos      
- [Use with react-redux (CodeSandbox)](https://codesandbox.io/s/n089y731x4)    
- [Use with react-router (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4)      
- [Examples from README file (CodeSandbox, React 14/15)](https://codesandbox.io/s/8ly7ml77y9)
- [Multiple Payment Methods (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4?module=/src/elements/MultiplePaymentMethods.js)      
- [Multiple Payment Methods Short version (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4?module=/src/elements/MultiplePaymentMethodsShort.js)      
- [Separated fields for the payment card method (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4?module=/src/elements/PaymentCardSeparated.js)      
- [Other Payment Methods (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4?module=/src/elements/OtherPaymentMethods.js)      
- [TypeScript (CodeSandbox)](https://codesandbox.io/s/j74n80zk5w)      
- [More examples from the E2E tests](test/e2e/fixtures)    

### Installation
Install using [Yarn](https://yarnpkg.com/en/):
```
yarn add @rebilly/framepay-react
```

Or using NPM:
```
npm install @rebilly/framepay-react --save
```

### Getting started

> The example described in this readme can be found [here (CodeSandbox)](https://codesandbox.io/s/8ly7ml77y9)

#### The FramePay context (`FramePayProvider`)
FramePayProvider provides settings to the FramePay API. See [Rebilly.initialize](https://rebilly.github.io/framepay-docs/reference/rebilly.html#rebilly-initialize) for a list of all configuration options.  

```jsx harmony
// index.js
import React from 'react';
import { render } from 'react-dom';
import { FramePayProvider } from 'framepay-react';

import MyCardPageComponent from './MyCardPageComponent';

const App = () => {
    return (
        <FramePayProvider injectStyle publishableKey="pk_sandbox_1234567890">
            <MyCardPageComponent/>
        </FramePayProvider>
    );
};

render(<App/>, document.getElementById('root'));

```
Define configuration parameters as attributes on the provider tag.
```html
publishableKey="pk_sandbox_1234567890"
```

#### Setting up your payment form

###### WARNING
Please, don't implementing the unmount functionality, use the examples.    

The react lifecycle methods already implemented in the library.  

###### withFramePay (All props)
- Rebilly - [FramePay's namespace](https://rebilly.github.io/framepay-docs/reference/rebilly.html)
- `BankAccountNumberElement`
- `BankAccountTypeElement`
- `BankRoutingNumberElement`
- `CardElement`
- `CardCvvElement`
- `CardExpiryElement`
- `CardNumberElement`

###### withFramePayCardComponent (Card props)
- Rebilly
- `CardElement`
- `CardCvvElement`
- `CardExpiryElement` 
- `CardNumberElement`

###### withFramePayBankComponent (Bank props)
- Rebilly
- `BankAccountNumberElement`
- `BankAccountTypeElement`
- `BankRoutingNumberElement`

##### With FramePay (`withFramePay`) HOC
This simple FramePay HOC is used to provide the `Rebilly` API in your component. It is most commonly used in combination with multiple payment methods.  

- [Payment cards and ACH (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4?module=/src/elements/MultiplePaymentMethods.js)      
- [Payment cards and ACH Short version (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4?module=/src/elements/MultiplePaymentMethodsShort.js)            
- [PayPal and Bitcoin (CodeSandbox)](https://codesandbox.io/s/z2q2lx9ry4?module=/src/elements/OtherPaymentMethods.js)      

##### Card elements (`withFramePayCardComponent`) HOC

Wrapper for the payment card features.
```jsx harmony
// MyCardPageComponent.js
import React from 'react';
import { withFramePayCardComponent } from 'framepay-react';

class MyCardPageComponent extends React.Component {
    constructor(props) {
        super(props);
        this.formNode = null;
        this.state = { firstName: '', lastName: '' };
        this.onSubmit = this.onSubmit.bind(this);
    }

    onSubmit(e) {
        e.preventDefault();
        // @see https://rebilly.github.io/framepay-docs/reference/rebilly.html#rebilly-createtoken
        this.props.Rebilly.createToken(
            this.formNode,
            { billingAddress: { ...this.state } }
        )
            .then(data => alert(JSON.stringify(data, null, 2)))
            .catch(err => alert(JSON.stringify(err, null, 2)));
    }

    render() {
        return (<form
            ref={node => this.formNode = node}
            onSubmit={this.onSubmit}>
            <div>
                <input
                    type="text"
                    name="firstName"
                    placeholder="First Name"
                    defaultValue={this.state.firstName}
                    onChange={(e) => this.setState({ firstName: e.target.value })}/>
            </div>
            <br/>
            <div>
                <input
                    type="text"
                    name="lastName"
                    placeholder="Last Name"
                    defaultValue={this.state.lastName}
                    onChange={(e) => this.setState({ lastName: e.target.value })}/>
            </div>
            <br/>
            <this.props.CardElement/>
            <hr/>
            <button>Make Payment</button>
        </form>);
    }
}

export default withFramePayCardComponent(MyCardPageComponent);
```

##### Bank elements (`withFramePayBankComponent`) HOC
Wrapper for the ACH features.
```jsx harmony
import React from 'react';
import { withFramePayBankComponent } from 'framepay-react';

class MyBankPageComponent extends React.Component {
    constructor(props) {
        super(props);
        this.formNode = null;
        this.state = { firstName: '', lastName: '' };
        this.onSubmit = this.onSubmit.bind(this);
    }

    onSubmit(e) {
        e.preventDefault();
        // @see https://rebilly.github.io/framepay-docs/reference/rebilly.html#rebilly-createtoken
        this.props.Rebilly.createToken(
            this.formNode,
            { billingAddress: { ...this.state } }
        )
            .then(data => alert(JSON.stringify(data, null, 2)))
            .catch(err => alert(JSON.stringify(err, null, 2)));
    }

    render() {
        return (<form
            ref={node => this.formNode = node}
            onSubmit={this.onSubmit}>
            <div>
                <input
                    type="text"
                    name="firstName"
                    placeholder="First Name"
                    defaultValue={this.state.firstName}
                    onChange={(e) => this.setState({ firstName: e.target.value })}/>
            </div>
            <br/>
            <div>
                <input
                    type="text"
                    name="lastName"
                    placeholder="Last Name"
                    defaultValue={this.state.lastName}
                    onChange={(e) => this.setState({ lastName: e.target.value })}/>
            </div>
            <br/>
            <this.props.BankElement/>
            <hr/>
            <button>Make Payment</button>
        </form>);
    }
}

export default withFramePayBankComponent(MyBankPageComponent);
```

### Advanced options

#### Initialization settings
The **framepay-react** package supports all the [FramePay](https://rebilly.github.io/framepay-docs) initialization settings.
See [Rebilly.initialize](https://rebilly.github.io/framepay-docs/reference/rebilly.html#rebilly-initialize) for all customizations.

Additionally `injectStyle` is available. When defined in the `FramePayProvider` it will add the default FramePay CSS in the header of your application. 

```jsx harmony
<FramePayProvider injectStyle publishableKey="pk_sandbox_1234567890">
    <MyCardPageComponent/>
</FramePayProvider>
```
The CSS file is hosted on Rebilly's CDN and is found at this URL: https://cdn.rebilly.com/framepay/v1/rebilly.css

See [adding default element styles](https://rebilly.github.io/framepay-docs/guide/#adding-default-element-styles) in FramePay's documentation for more details.

#### Create Token Parameters
The `createToken` method supports all FramePay arguments. See [Rebilly.createToken](https://rebilly.github.io/framepay-docs/reference/rebilly.html#rebilly-createtoken) for more details.

### Troubleshooting

The methods `withFramePay`, `withFramePayCardComponent` and `withFramePayBankComponent` are [higher-order-components](https://reactjs.org/docs/higher-order-components.html). They can't be called directly from your `render()` method, so assign the generated component to a variable in the global scope before use.

##### Incorrect
```jsx harmony
import * as React from 'react';
import {withFramePayCardComponent} from 'framepay-react'

class SomeComponent extends React.Component {
    render(){
        return(<div>
            {withFramePayCardComponent(MyCardComponent)}
        </div>)
    }
}
```

##### Correct
```jsx harmony
import * as React from 'react';
import {withFramePayCardComponent} from 'framepay-react'

const MyCardElement = withFramePayCardComponent(MyCardComponent);

class SomeComponent extends React.Component {
    render(){
        return(<div>
            <MyCardElement />
        </div>)
    }
}
```
