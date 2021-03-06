# Payment.js

Port from [jQuery.payment](https://github.com/stripe/jquery.payment) without jQuery.

A general purpose library for building credit card forms, validating inputs and formatting numbers.

For example, you can make a input act like a credit card field (with number formatting and length restriction):

``` javascript
payment('formatCardNumber', document.querySelector('input.cc-num'));
```

Then, when the payment form is submitted, you can validate the card number on the client-side:

``` javascript
var valid = payment.validateCardNumber(document.querySelector('input.cc-num').value);

if (!valid) {
  alert('Your card is not valid!');
  return false;
}
```

You can find a full [demo here](http://mukhortov.github.io/payment.js/example).

Supported card types are:

* Visa
* MasterCard
* American Express
* Diners Club
* Discover
* UnionPay
* JCB
* Visa Electron
* Maestro
* Forbrugsforeningen
* Dankort

## API

### payment('formatCardNumber', inputElement)

Formats card numbers:

* Includes a space between every 4 digits
* Restricts input to numbers
* Limits to 16 numbers
* Supports American Express formatting
* Adds a class of the card type (e.g. 'visa') to the input

Example:

``` javascript
payment('formatCardNumber', document.querySelector('input.cc-num'));
```

### payment('formatCardExpiry', inputElement)

Formats card expiry:

* Includes a `/` between the month and year
* Restricts input to numbers
* Restricts length

Example:

``` javascript
payment('formatCardExpiry', document.querySelector('input.cc-exp'));
```

### payment('formatCardCVC', inputElement)

Formats card CVC:

* Restricts length to 4 numbers
* Restricts input to numbers

Example:

``` javascript
payment('formatCardCVC', document.querySelector('input.cc-cvc'));
```

### payment('restrictNumeric', inputElement)

General numeric input restriction.

Example:

``` javascript
payment('restrictNumeric', document.querySelector('[data-numeric]'));
```

### payment.validateCardNumber(number)

Validates a card number:

* Validates numbers
* Validates Luhn algorithm
* Validates length

Example:

``` javascript
payment.validateCardNumber('4242 4242 4242 4242'); //=> true
```

### payment.validateCardExpiry(month, year)

Validates a card expiry:

* Validates numbers
* Validates in the future
* Supports year shorthand

Example:

``` javascript
payment.validateCardExpiry('05', '20'); //=> true
payment.validateCardExpiry('05', '2015'); //=> true
payment.validateCardExpiry('05', '05'); //=> false
```

### payment.validateCardCVC(cvc, type)

Validates a card CVC:

* Validates number
* Validates length to 4

Example:

``` javascript
payment.validateCardCVC('123'); //=> true
payment.validateCardCVC('123', 'amex'); //=> true
payment.validateCardCVC('1234', 'amex'); //=> true
payment.validateCardCVC('12344'); //=> false
```

### payment.cardType(number)

Returns a card type. Either:

* `visa`
* `mastercard`
* `amex`
* `dinersclub`
* `discover`
* `unionpay`
* `jcb`
* `visaelectron`
* `maestro`
* `forbrugsforeningen`
* `dankort`

The function will return `null` if the card type can't be determined.

Example:

``` javascript
payment.cardType('4242 4242 4242 4242'); //=> 'visa'
```

### payment.cardExpiryVal(string) and payment('cardExpiryVal', inputElement)

Parses a credit card expiry in the form of MM/YYYY, returning an object containing the `month` and `year`. Shorthand years, such as `13` are also supported (and converted into the longhand, e.g. `2013`).

``` javascript
payment.cardExpiryVal('03 / 2025'); //=> {month: 3: year: 2025}
payment.cardExpiryVal('05 / 04'); //=> {month: 5, year: 2004}
payment('cardExpiryVal', document.querySelector('input.cc-exp')) //=> {month: 4, year: 2020}
```

This function doesn't perform any validation of the month or year; use `payment.validateCardExpiry(month, year)` for that.

## Example

Look in [`./example/index.html`](example/index.html)

## Building

Run `cake build`

## Running tests

Run `cake test`

## Autocomplete recommendations

We recommend you turn autocomplete on for credit card forms, except for the CVC field (which should never be stored). You can do this by setting the `autocomplete` attribute:

``` html
<form autocomplete="on">
  <input class="cc-number">
  <input class="cc-cvc" autocomplete="off">
</form>
```

You should also mark up your fields using the [Autocomplete Types spec](http://wiki.whatwg.org/wiki/Autocomplete_Types). These are respected by a number of browsers, including Chrome.

``` html
<input type="text" class="cc-number" pattern="\d*" autocomplete="cc-number" placeholder="Card number" required>
```

Set `autocomplete` to `cc-number` for credit card numbers and `cc-exp` for credit card expiry.

## Mobile recommendations

We recommend you set the `pattern` attribute which will cause the numeric keyboard to be displayed on mobiles:

``` html
<input class="cc-number" pattern="\d*">
```

You may have to turn off HTML5 validation (using the `novalidate` form attribute) when using this `pattern`, as it won't match space formatting.
