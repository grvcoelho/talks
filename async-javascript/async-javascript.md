const getTotal = (orderId, callback) => {
  getProducts(orderId, (err, products) => {
    getStock(products, (err, hasStock) => {
      if (hasStock) {
        calculateTotal(products, (err, total) => {
          callback(null, total)
        })
      } else {
        callback(err, null)
      }
    })
  })
}

const finishOrder = (callback) => {
  getTotal(123, (err, total) => {
    getShipping(total, (err, shipping) => {
      confirmPurchase((err, message) => {
        console.log(message)
        callback(message)
      })
    })
  })
}

const finishOrder = (orderId, callback) => {
  getProducts(orderId, (err, products) => {
    getStock(products, (err, hasStock) => {
      calculateTotal(products, (err, total) => {
        getShipping((err, shipping) => {
          confirmPurchase(orderId, shipping, (err, success) => {
            callback(null, success)
          })
        })
      })
    })
  })
}

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







listen( "click", function handler(evt){
	setTimeout( function request(){
		ajax( "http://some.url.1", function response(text){
			if (text == "hello") {
				handler();
			}
			else if (text == "world") {
				request();
			}
		} );
	}, 500) ;
} );

listen('click', function handler (event) {
  setTimeout(function request () {
    fetch('/api/message', function (response) {
      if (text === 'hello') {
        return handler()
      }

      return request()
    })
  }, 500)
})


const getUserFriends = (userId, callback) => {
  fetch(`/users/${userId}`, (err, user) => {
    const friends = []
  })
}

const getUserFriends = (userId) => {
  return fetch(`/users/${userId}/friends`)
    .then(friendIds => {
      const promises = friendIds.map(id => fetch(`/users/${id}`))

      return Promise.all(promises)
    })
    .then((friends) => console.log('friends'))
}



# Javascript is

* Synchronous
* Non-blocking I/O
* Concurrent
* Single-threaded
* Part of your program runs now, and another part of your program runs later


# Callbacks

* Never Calling the Callback
  *Calling Too Few or Too Many Times

* Callbacks are the fundamental unit of asynchrony in JS
* Callbacks express asynchronous flow in a rather nonlinear, nonsequential way
* To get steps 2, 3, and 4 linked together so they happen in succession, the only affordance callbacks alone gives us is to hardcode step 2 into step 1, step 3 into step 2, step 4 into step 3, and so on.
* Node.js was built on top of callbacks
* You can save a reference of a function in a variable when using JavaScript. Then you can use them as arguments of another function to execute later. This is our “callback”.
* Callback hell
* Inversion of control (call to early, call to later (never), call many times, call few times)
* Sync and Async functions (some functions can be sync and some others may be not)
  *
listen("click", function handler(evt){
	setTimeout(function request(){
		ajax("http://some.url.1", function response(text){
			if (text == "hello") {
				handler();
			}
			else if (text == "world") {
				request();
			}
		} );
	}, 500) ;
} );

analytics.trackPurchase(purchaseData, () => {
  chargeCreditCard()
  displayThankyouPage()
})

analytics.trackPurchase( purchaseData, function(){
  chargeCreditCard();
  displayThankyouPage();
});

# Promises

* The Hamburger analogy
  * Promise.all, Promise.race
  * Uncancellable
  *They don't get rid of callbacks, they just redirect the orchestration of those callbacks to a trustable intermediary mechanism that sits between us and another utility.
* A representation of a future value
* Time-independent wrapper around a value
* Promises are `chainable`
* States: `Pending`, `Fulfilled`, `Rejected`
* A Promise state cannot be changed
* Can only be resolved once (memoize example?)
* In every .then function call, a new Promise is returned when the previous callback is done
* You can’t abort a chain of promises

# Generators + Coroutines

* Generators make it possible to pause and resume function execution
* Your brain works at some level `sync` (non-scientific statement)
* It's easier to understand something `linear` and `sequentially`
* Generators + Promises allow for a `sync-sequential` looking code, even though it really still is fundamentally async.
* Concurrency. Promise.map runs concurrently, show some pattern of when its more optimal to use Promise.all then waiting each request with yield individually.


const coroutine = (fn) => {
  const gen = fn()
  const doNext = (data) => {
    const next = gen.next(data)

    if (!next.done) {
      return Promise.resolve(next.value).then(doNext)
    }
  }

  return doNext()
}

const calculateTotal = coroutine(function* () {
  const quantity = yield getQuantity()
  const unitPrice =  yield getUnitPrice()

  return unitPrice * quantity
})

# Async/Await

Write `async` code that flows like `sync` code.
Inside a function marked as `async`, you are allowed to place the `await` keyword in front of an expression that returns a promise. When you do, the execution of the `async` function is paused until the promise is resolved.
Async/Await + Promises = <3

# Bonus: Reactive Extensions

buyHamburger()
  .then(hamburger => {
    return eatHamburger()
  })
  .catch(err => {
    console.log(err)
    return cryALot()
  })




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

const finishOrder = (orderId) => {
  return getProducts(orderId)
    .then(products => {
      return getStock(products)
    })
}

const finishOrder = (orderId) => {
  return getProducts(orderId)
    .then(getStock)
    .then(calculateTotal)
    .then(getShipping)
    .then(confirmPurchase)
}

finishOrder(123)
  .then(message => {
    displayThankyouPage()
  })
