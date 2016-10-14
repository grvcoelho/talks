build-lists: true
slidenumbers: true

<!-- ![](http://fm.cnbc.com/applications/cnbc.com/resources/img/editorial/2014/05/13/101666673-US_dollar_2.1910x1000.jpg) -->

### :credit_card: :iphone: :dollar:
# <br>
## Payment Request API

---

![inline](https://cloud.githubusercontent.com/assets/7416751/19373165/637be3ee-9198-11e6-901d-ff6e00ada68a.png)

## [fit] Guilherme Rv Coelho
### @grvcoelho
![inline 30%](https://cloud.githubusercontent.com/assets/7416751/19373244/f6c73676-9198-11e6-9501-ed992e8f5c0e.png)

---

### :handbag: :iphone:
# <br>
### Buying online should be easy and simple

---

### :globe_with_meridians:
# <br>
### `>`50% of mobile purchases
### are made through **websites** [^1]

[^1]: Source: The State of Mobile Commerce 2016 - CRITEO 2016

---

### :iphone:
# <br>
### Apps convert
### 3x more than **websites** [^1]

[^1]: Source: The State of Mobile Commerce 2016 - CRITEO 2016

---

# Why?

![inline 80% 100%](https://cloud.githubusercontent.com/assets/7416751/19321536/d14afc7e-908b-11e6-9cb1-8bf17014d0bf.png)

### :sweat:

---

![fit](https://www.smashingmagazine.com/images/checkout-pages-showcase/shopcomposition.png)

---

![fit](https://cloud.githubusercontent.com/assets/7416751/19372744/01556468-9195-11e6-96e2-8346b31f3fa5.gif)

---

![fit](https://cloud.githubusercontent.com/assets/7416751/19372741/ff31b09c-9194-11e6-81f5-70361b50a83f.png)

----

![fit](https://cloud.githubusercontent.com/assets/7416751/19372717/df9e537a-9194-11e6-86f5-5989a293474e.png)

---

### :computer:
# <br>
### Introducing

## Payment Request API

---

![fit](https://cloud.githubusercontent.com/assets/7416751/19323986/c345085e-9095-11e6-97e2-3773e29f2753.png)

---

![](https://media.giphy.com/media/3o6Zt46ATnuZcypHpK/giphy.gif)

---

![inline](https://cloud.githubusercontent.com/assets/7416751/19322597/168f5e5c-9090-11e6-8945-00511433cbd7.png)

https://w3c.github.io/browser-payment-api/

^ It's an API that is being developed

---

# Goals

* To let the browser be an intermediary among merchants, users, and payment methods
* To standardize the payment communication flow as much as possible
* To seamlessly support different secure payment methods
* To work on any browser, device, or platform—mobile or otherwise

---

# Non-Goals

* Not trying to create a new payment method
* Not trying to integrate directly with payment processors

---

# Payment Transaction Process

![inline](https://cloud.githubusercontent.com/assets/7416751/19322972/62470b96-9091-11e6-9649-90eafc785b00.png)

---

![filtered](https://media.giphy.com/media/26gJzWq8i69cxZFD2/giphy.gif)

# Computer Time

---

![fit left](https://cloud.githubusercontent.com/assets/7416751/19389049/c3f63df4-91f8-11e6-9608-25fcaf8df409.jpg)

https://grvcoelho.github.io/payment-request

---

### 1. Create a payment request instance

---

```javascript
let request = new PaymentRequest(
  supportedInstruments,
  details,
  options
)
```

---

```javascript
let supportedInstruments = [{
  supportedMethods: ['visa', 'mastercard']
}]
```
---

```javascript
let details = {
  displayItems: [{
    label: 'Original subscription amount',
    amount: { currency: 'BRL', value : '65.00' }
  }, {
    label: 'Friends and family discount',
    amount: { currency: 'BRL', value : '-10.00' }
  }],

  total:  {
    label: 'Total',
    amount: { currency: 'BRL', value : '55.00' }
  }
}
```

---

```javascript
let options = {
  requestShipping: true,
  requestPayerEmail: true,
  requestPayerPhone: true
}
```

---

### 2. Show the native UI <br>with `.show()`

---

```javascript
request.show()
  .then(payment => {
    // User pressed "Pay"
  })
```

---

![fit](https://cloud.githubusercontent.com/assets/7416751/19323986/c345085e-9095-11e6-97e2-3773e29f2753.png)

---

### 3. Process the payment

---

```javascript
request.show().then(result => {
  let payload = {
    amount: 5500,
    encryption_key: PAGARME_ENCRYPTION_KEY,
    card_number: result.details.cardNumber,
    card_holder_name: result.details.cardholderName,
    card_cvv: result.details.cardSecurityCode,
    card_expiration_date: result.details.expiryMonth + result.details.expiryYear.substr(2, 2),
  }

  return fetch('https://api.pagar.me/1/transactions', {
    method: 'POST',
    body: JSON.stringify(payload)
  })
})
```

---

![fit](https://cloud.githubusercontent.com/assets/7416751/19389228/810be8e4-91f9-11e6-924f-67869babc510.png)
![fit](https://cloud.githubusercontent.com/assets/7416751/19389187/4c3c6562-91f9-11e6-8c1e-104964654cdd.png)


---


### 4. Display Payment Results

---

```javascript
fetch('https://api.pagar.me/1/transactions', {
  method: 'POST',
  body: JSON.stringify(payload)
})
  .then(res => {
    if (res.ok) {
      result.complete('success')
    }

    return result.complete('fail')
  })
```

---

### 5. Make it **progressive**

---

```javascript
if (window.PaymentRequest) {
  // browsers that support payment request
} else {
  // browsers that don't support payment request
  pagarme.checkout.open()
}
```

---

### :closed_lock_with_key:
# <br>
### Works only over https

---

```sh
$ npm install webpack-dev-server
```

```javascript
// package.json
"scripts": {
  "start": "webpack-dev-server --https --port 3000"
}
```

---

### :iphone:
# <br>
### Works only on mobile

---

### :globe_with_meridians: :white_check_mark:
# <br>
### Chrome 53+
### Browser Support

---

![inline](https://cloud.githubusercontent.com/assets/7416751/19373450/59506e7e-919a-11e6-97a1-417d7e3c4f03.png)

https://github.com/grvcoelho/payment-request

---

![inline](https://cloud.githubusercontent.com/assets/7416751/19373165/637be3ee-9198-11e6-901d-ff6e00ada68a.png)

# Thanks
![inline 30%](https://cloud.githubusercontent.com/assets/7416751/19373244/f6c73676-9198-11e6-9501-ed992e8f5c0e.png)

