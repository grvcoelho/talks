footer: **grvcoelho** @ pagar.me 2017
slidenumbers: true

![inline 30%](https://cloud.githubusercontent.com/assets/7416751/25398731/616d7dc4-29c3-11e7-9b96-7052f32bde07.png)

# Journey Into AWS

## SQS - Simple Queue Service

---

# Who am I?

### :man: Guilherme Coelho
### :house_with_garden: Jundiaí
### :office: Pagar.me

---

## What is SQS?

---

## What is SQS (Simple Queue Service)?

- It's a **Service**!
- It's about **Queues**!
- It's **Simple**!

---

## Why?

---

## Why would you use it?

- :mailbox: Decoupling the components of an application.
- :battery: Decoupling heavy-work processing.

---

![inline](https://cloud.githubusercontent.com/assets/7416751/25407458/bb6c34b0-29e0-11e7-8e49-0f2332c9665f.png)

---

## Anatomy of a Queue :skull::microscope:

---

## Anatomy of a Queue :skull::microscope:

- `QueueUrl`
- Messages:
  - `MessageID`
  - `ReceiptHandle`

---

## How Queues Work?

---


## How Queues Work

![inline 100%](https://cloud.githubusercontent.com/assets/7416751/25410213/cdcb58d8-29eb-11e7-865e-00fee0b98ee0.png)

---

## At-Least-Once Delivery

- The same message can be delivered multiple times.
- Your application must have **Idempotency**.
- Idempotency: process multiple times, no harm done.

---

## In-flight Messages and Visibility Timeout

![inline 100%](https://cloud.githubusercontent.com/assets/7416751/25410721/ed976c3a-29ee-11e7-84f6-4f5c46d261a8.png)

---

## Changing Message Visibility

- Extending VisibilityTimeout
  - `ChangeMessageVisibility(15)`
- Terminating VisibilityTimeout
  - `ChangeMessageVisibility(0)`

---

## Message Lifecycle

---

## Message Lifecyle: post

![inline 100%](https://cloud.githubusercontent.com/assets/7416751/25412291/1569de92-29f8-11e7-8f4c-b33475ab29bc.png)

---

## Message Lifecycle: receive

![inline 100%](https://cloud.githubusercontent.com/assets/7416751/25412293/15f811d0-29f8-11e7-8b63-8dcb3e94bf5c.png)

---

## Message Lifecycle: delete

![inline 100%](https://cloud.githubusercontent.com/assets/7416751/25412292/15f6ad0e-29f8-11e7-9fdc-ddd96bbe1e31.png)

---

## Delay Queues :clock10:

---

## Delay Queues :clock10:

![inline 100%](https://cloud.githubusercontent.com/assets/7416751/25412539/b2d0fa70-29f9-11e7-81cb-17a09dd51ae6.png)

---

## Dead Letter Queues :mailbox_with_mail::skull:

---

## Dead Letter Queues :mailbox_with_mail::skull:

- Messages that can't be processed.
- `RedrivePolicy`
  - `deadLetterTargetArn`
  - `maxReceiveCount`

---

## Short vs Long Polling :swimmer:

---

## Short vs Long Polling :swimmer:

- **Short Polling (default)**: does not wait for messages to come.
- **Long Polling**: wait `n` seconds for messages to come.
  - Queries **all** the servers
  - Less empty responses
  - Reduced costs

---

## Process Queue Pattern

---

## Process Queue Pattern

![inline](https://cloud.githubusercontent.com/assets/7416751/25409523/a78fa582-29e8-11e7-8ff2-0e070017fa2c.png)

---

## We can make this easier :telescope:

---

![inline](https://cloud.githubusercontent.com/assets/7416751/25409676/4eb7a134-29e9-11e7-9573-a33c5fad11b1.png)

#### [https://github.com/pagarme/sqs-quooler](https://github.com/pagarme/sqs-quooler)

---

## sqs-quooler: installing

```sh
$ npm install --save sqs-quooler
```

---

## sqs-quooler: importing

```javascript
import { Queue } from 'sqs-quooler'

const myQueue = new Queue({
  endpoint: 'https://sqs.amazonaws.com/queue/my-queue'
  concurrency: 10
})
```

---

## sqs-quooler: pushing items

```javascript
myQueue.push({ id: 123 })
myQueue.push({ user: 'wx-78' })
```

---

## sqs-quooler: processing the queue

```javascript
const handler = (parsedItem, rawSQSMessage) => {
  // Process the item
  return processItem (parsedItem)
}

const options = {
  oneShot: false,
  keepMessage: false
}

myQueue.startProcessing(handler, options)
```

---

## FIFO x Standard Queues

---

## FIFO x Standard

|  | Standard | FIFO |
| --- | --- | --- |
| Delivery | At-least-once | Exactly-once |
| Ordered | :x: | :white_check_mark: |
| High Throughput  | :white_check_mark: | :x: (300 t/s) |
| Price[^1] | :dollar::dollar: | :dollar::dollar::dollar:|

[^1]: Standard Queue Pricing: _$0,40 per 1 million requests_. [[source]](https://aws.amazon.com/sqs/pricing/)

---

## :football: Thanks :football:

### [https://github.com/grvcoelho/talks](https://github.com/grvcoelho/talks)
