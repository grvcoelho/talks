build-lists: true

# [fit] Payment Request API

---

### My name is @grvcoelho

---

# Introduction

Buying goods online is a convenient but often frustrating experience, particularly on mobile devices. Although mobile traffic continues to increase, mobile conversions account for only about a third of all completed purchases. In other words, users abandon mobile purchases twice as often as desktop purchases.

---

66% of mobile
purchases are made through **websites** rather than apps

---

conversion rate on mobile websites is only 33% of that on desktop

---

# Why?

![inline 80% 100%](https://cloud.githubusercontent.com/assets/7416751/19321536/d14afc7e-908b-11e6-9cb1-8bf17014d0bf.png)

---

![inline](https://cloud.githubusercontent.com/assets/7416751/19321808/e4086418-908c-11e6-856f-a6695cc50d0c.png)

https://pagar.me/checkout

---

### Introducing

## Payment Request API

---

![inline](https://cloud.githubusercontent.com/assets/7416751/19322597/168f5e5c-9090-11e6-8945-00511433cbd7.png)

https://w3c.github.io/browser-payment-api/

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

![filtered](https://media.giphy.com/media/wErJXg1tIgHXG/giphy.gif)

# Show me the Code!

---

### TODO: picture of what we're gonna do

wrbsite

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

---

---



