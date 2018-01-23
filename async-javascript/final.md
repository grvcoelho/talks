# Javascript

* Non-blocking I/O
* Single-threaded
* Concurrent
* Synchronous

---

# Async

* Part of your program runs `now`
* Part of your program runs `later`

---

# Featuring

* Callbacks
* Promises
* Generators + Coroutines
* Async/Await
* Reactive Extensions

---

go
func main() {
	fmt.Println("BEGIN")

	contents, _ := ioutil.ReadFile("/tmp/dat")
	fmt.Print(string(contents))

	fmt.Println("FINISH")
}


---


const fs = require('fs')

console.log('BEGIN')

fs.readFile('/tmp/dat', (err, contents) => {
  console.log(String(contents))
})

console.log('FINISH')


---

# Callbacks

* Node.js is built on top of it
* Fundamental unit of asynchrony in Javascript
* Pass a function to another function that will `call it back`


const fs = require('fs')

console.log('BEGIN')

fs.readFile('/tmp/dat', (err, contents) => {
  console.log(String(contents))
})

console.log('FINISH')


---


const readFile = (filename, callback) => {
  system.openAndReadFile(filename, (err, data) => {
    if (err) {
      return callback(err, null)
    }

    callback(null, String(data))
  })
}

readFile('/tmp/dat', (err, contents) => {
  console.log(contents)
})


---


const divide = (a, b, callback) => {
  if (b === 0) {
    return callback('Cannot be zero', null)
  }

  const result = a / b

  callback(null, result)
}

divide(10, 2, (err, result) => {
  // ...
})


---


const finishOrder = (orderId, callback) => {
  getProducts(orderId, (err, products) => {
    getStock(products, (err, hasStock) => {
      calculateTotal(products, (err, total) => {
        getShipping((err, shipping) => {
          confirmPurchase(orderId, shipping, (err, message) => {
            callback(null, message)
          })
        })
      })
    })
  })
}

finishOrder(123, (err, message) => {
  // ...
})


---

# Callback Hell

* Indentation*
* Poor error handling
* Asynchronous flow in `nonlinear`, `nonsequantial` way
* **Inversion of control**

---


analytics.trackPurchase(purchaseData, () => {
  chargeCreditCard()
  displayThankYouPage()
})


---

# Callback Problems

* Sync and async callbacks
* Error swallowing
* Never calling the callback
* Calling the callback multiple times

---

# Promises

* A representation of a future value
* Time-independent wrapper around a value
* The Hamburger Analogy

---


buyHamburger()
  .then(hamburger => {
    return eatHamburger(hamburger)
  })
  .catch(err => {
    console.log(err)
    return cryALot()
  })


---


const finishOrder = (orderId) => {
  return getProducts(orderId)
    .then(getStock)
    .then(calculateTotal)
    .then(getShipping)
    .then(confirmPurchase)
}

finishOrder(123)
  .then(message => {
    displayThankYouPage()
  })

---

const calculateTotal = (product, quantity) => {
  return new Promise((resolve, reject) => {
    fetch('/products/${product.id}/price', (err, price) => {
      if (err) {
        return reject(err)
      }

      const total = price * quantity

      return resolve(total)
    })
  })
}

calculateTotal(xbox, 2)
  .then(total => {
    return chargeCreditCard(total)
  })
  .then(() => {
    return displayThankYouPage()
  })
  .catch(err => {
    return displayErrorPage(err)
  })


---

# Promises

* Have 3 states: `Pending`, `Fulfilled`, `Rejected`
* The state never changes from `Fulfilled` or `Rejected`
* Can only be resolved once

---

# Promises

* Promises are chainable
* Every `.then` call returns a new Promise
* Rejected values go until they find a `.catch`

---

# Promise API

* `Promise.all`
* `Promise.race`
* `Promise.reject`
* `Promise.resolve`

---

# Promise Problems

* Uncancellable
* Aborting a chain of Promises

---

findOrder(123)
  .then(order => {
    return findItems(order)
  })
  .then(items => {
    return findCustomer(order) // no order here
  })

---

findOrder(123)
  .then(order => {
    return Promise.all([order, findItems(order)])
  })
  .then(([order, items]) => {
    return Promise.all([
      order,
      items,
      findCustomer(order)
    ])
  })
  .then(([order, items, customer]) => {
    return chargeOrder(order, items, customer)
  })

---

findOrder(123)
  .then(order => {
    return findItems(order)
      .then(items => {
        return findCustomer(order)
          .then(customer => {
            return chargeOrder(order, items, customer)
          })
      })
  })

---

const handleOrder = (orderId) => {
  const order = findOrder(orderId)
  const items = findItems(order)
  const customer = findCustomer(order)

  return chargeOrder(order, items, customer)
}

---

# Generators

* Generators make it possible to `pause` and `resume` function execution
* Your brain works, at some level, `sync` (non-scientific statement)
* It's easier to understand something `linear` and `sequential`

---

function* foo (x) {
  const y = x * (yield "TAKE CONTROL")
  return y
}

const it = foo(6)

// { done: false, value: "TAKE CONTROL" }
it.next()

// { done: true, value: 42 }
it.next(7)

---

const coroutine = (gen) => {
  const it = gen()

  const doNext = (data) => {
    const next = it.next(data)

    if (!next.done) {
      return Promise.resolve(next.value).then(doNext)
    }

    return next.value
  }

  return doNext()
}

---

const handleOrder = coroutine(function* (orderId) {
  const order = yield findOrder(orderId)
  const items = yield findItems(order)
  const customer = yield findCustomer(order)

  return chargeOrder(order, items, customer)
})

---

const handleOrder = coroutine(function* (order) {
  const items = yield findItems(order)
  const customer = yield findCustomer(order)

  return chargeOrder(order, items, customer)
})

// vs

const handleOrder = (order) => {
  return Promise.all([
    findItems(order),
    findCustomer(order)
  ])
    .then(([items, customer]) => {
      return chargeOrder(order, items, customer)
    })
}

---

const handleOrder = coroutine(function* (orderId) {
  const order = yield findOrder(orderId)
  const [items, customer] = yield Promise.all([
    findItems(order),
    findCustomer(order)
  ])

  return chargeOrder(order, items, customer)
})

---

let response

before(() => {
  return request('/orders')
    .then(response => {
      response = response
    })
})

it('should test request', () => {
  assert(response.status).is(200)
})

// vs

it('should test request', coroutine(function* () {
  const response = yield request('/orders')
  assert(response.status).is(200)
}))
