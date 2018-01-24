# Javascript

* Non-blocking I/O
* Single-threaded
* Concurrent
* Synchronous

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

runA(function () {
  runB()

  runC(function () {
    runD()
  })

  runE()
})

runF()

---

async.waterfall([
  function(callback) {
    callback(null, 'one', 'two')
  },
  function(arg1, arg2, callback) {
    // arg1 now equals 'one' and arg2 now equals 'two'
    callback(null, 'three')
  },
  function(arg1, callback) {
    // arg1 now equals 'three'
    callback(null, 'done')
  },
], function (err, result) {
  // result now equals 'done'
})


http.get('/users')
  .success(users => {})
  .fail(err => {})

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

calculateTotal(xbox, 2)
  .then(chargeCreditCard)
  .then(displ)


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

Promise.resolve(5)
  .then(x => {
    return x * 2
  })
  .then(result => {
    return anotherPromise()
  })


Promise.resolve(5)
  .then(x => {
    if (x === 5) {
      return Promise.reject(new Error('Value not allowed'))
    }

    if (Number.isNaN(Number(x))) {
      throw new TypeError('Must be a number')
    }

    return x
  })
  .then(success)
  .catch(err => {
    console.log(err.message)
  })


console.log('Hello')

Promise.all([
  findOrder(123),
  findCustomer('John Doe')
])
  .then([order, customer] => {
    return chargeCreditCard(order, customer)
  })

Promise.race([
  findAddressOnCorreios(01329010),
  findAddressOnDatabase(01329010)
])
  .then(address => {
    // ...
  })

const timeout = (ms) => new Promise((resolve, reject) => {
  setTimeout(
    () => reject(new TimeoutError())
    ms
  )
})

Promise.race([
  timeout(3000),
  chargeCreditCard()
])
  .then(displaySuccess)
  .catch(handleTimeout)


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

---

# Async / Await

* Works just like Generators + Coroutines
* Also pauses function execution
* Is built on top of `Promises`
* Write `async` code that flows like `sync` code
* Returns a `Promise`
* It doesn't trasnpile well with **Babel**

---

const handleOrder = coroutine(function* (orderId) {
  const order = yield findOrder(orderId)
  const items = yield findItems(order)
  const customer = yield findCustomer(order)

  return chargeOrder(order, items, customer)
})

---

const handleOrder = async function (orderId) {
  const order = await findOrder(orderId)
  const items = await findItems(order)
  const customer = await findCustomer(order)

  return chargeOrder(order, items, customer)
}

---

const fn = async function () {
  const values = [2, 5]

  const result = values.map(async function (x) {
    return await double(x)
  })

  return result
}

# Bonus: Reactive Programming

* Aynchronous streams of events and data
* Intervals, webSockets, DOM events, general events
* Single vs Multiple values
* Cancellable vs Uncancellable
* Eager vs Lazy

---

const numberPromise = new Promise((resolve) => {
    resolve(5)
    resolve(10)
})

numberPromise.then(value => console.log(value))

---

const numberObservable = new Observable((observer) => {
  observer.next(5)
  observer.next(10)
})

numberObservable.subscribe(value => console.log(value))

---

const Rx = require('rx')

Rx.Observable()
  .interval(1000)
  .map(x => x + 1)
  .takeWhile(x => x <= 3)
  .concat(Rx.Observable.of('World'))
  .subscribe(x => console.log(`Hello ${x}!`))
