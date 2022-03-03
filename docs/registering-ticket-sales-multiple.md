# Registering multiple ticket sales at once

This mini-guide illustrates how to register UiTPAS discounted ticket sales for multiple tickets at once. It builds upon the [Registering ticket sales guide](/docs/registering-ticket-sales-group.md) which you need to read first to learn more about authentication, the work flow and registering events.

### 1. Determine possible UiTPAS tariffs

The register ticket sale request accepts an array of ticket sales. This array can contain multiple ticket sales for:

*   one passholder for multiple events
*   multiple passholders for one event
*   or even multiple passholders for multiple events

It is important to pass TicketSale objects with tariffs that are applicable to that passholder, for the given event. Do note that the register ticket sale request will fail for the complete set of ticket sales if one of those ticket sales would trigger an error.

To determine the available tariffs, your application needs to use [GET /tariffs](/reference/uitpas.json/paths/~1tariffs/get) for every passholder/event combination for which you need to register a ticket sale.

Example request for passholder `0900000672312` and event `5a0967f9-cc06-4c3c-9206-30481a767434`:

```http
GET /tariffs/?eventId=5a0967f9-cc06-4c3c-9206-30481a767434&uitpasNumber=0900000672312&regularPrice=10 HTTP/1.1
Host: https://api.uitpas.be
Authorization: Bearer YOUR_CLIENT_ACCESS_TOKEN'
```

Have a look at the [test dataset](/docs/test-dataset) for more sample passholders or events.

Example response:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "available": [
    {
      "id": "SOCIALTARIFF",
      "name": "Kansentarief",
      "price": 2,
      "type": "SOCIALTARIFF",
      "remaining": 1
    },
    {
      "id": "COUPON_1079",
      "name": "[TEST] €1 voor een evenement voor tieners (GENT)",
      "price": 1,
      "type": "COUPON",
      "remaining": 1
    }
  ]
}
```

Example request for passholder `0900010888817` and event `5a0967f9-cc06-4c3c-9206-30481a767434`:

```http
GET /tariffs/?eventId=5a0967f9-cc06-4c3c-9206-30481a767434&uitpasNumber=0900010888817&regularPrice=10 HTTP/1.1
Host: https://api.uitpas.be
Authorization: Bearer YOUR_CLIENT_ACCESS_TOKEN'
```

Example response:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "available": [
    {
      "id": "COUPON_1079",
      "name": "[TEST] €1 voor een evenement voor tieners (GENT)",
      "price": 1,
      "type": "COUPON",
      "remaining": 1
    }
  ]
}
```

### 2. Register the ticket sales

After the end-user has selected an UiTPAS tariff for every UiTPAS number and event combination, your website or application continues with its regular flow for completing the sale like payment (for the discounted price) etc.

When your regular flow successfully finishes, you need to [register the ticket sales](/reference/uitpas.json/paths/~1ticket-sales/post). If you don't register the ticket sale correctly, the organizer can not get reimbursed for the discount within the UiTPAS financial flow.

For example, registering ticket sales for UiTPAS number `0900000672312` using the social tariff and `0900010888817` using a coupon tariff for event `5a0967f9-cc06-4c3c-9206-30481a767434`:

```http
POST /ticket-sales HTTP/1.1
Content-Type: application/json
Host: https://api.uitpas.be
Authorization: Bearer YOUR_ACCESS_TOKEN'

[
  {
    "regularPrice": 10,
    "tariff": {
      "id": "SOCIALTARIFF"
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900000672312"
  },
  {
    "regularPrice": 10,
    "tariff": {
      "id": "COUPON_1079"
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900010888817"
  }  
]
```

Example response:

```http
200 OK HTTP/1.1
Content-Type: application/json

[
  {
    "id": "499860",
    "tariff": {
      "id": "SOCIALTARIFF",
      "name": "Kansentarief",
      "price": 2,
      "type": "SOCIALTARIFF"
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900000672312"
  },
  {
    "id": "499861",
    "tariff": {
      "id": "COUPON_1079",
      "name": "[TEST] €1 voor een evenement voor tieners (GENT)",
      "price": 1,
      "type": "COUPON",
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900010888817"
  }  
]
```

> Note that the response contains an id for every registered ticket sale. **We advise you to store this id** in your application in case you need to cancel the ticket sale later.

<!-- theme: warning -->

> **If one of the ticket sales is invalid** (for example the chosen tariff is incorrect or expired), **none of the ticket sales will be registered**. You will instead get an error response with more details about the problem, and can then retry the registration without the incorrect ticket sales or ask the passholder to change the tickets and/or tariffs that they want.

### 7. Cancelling the ticket sales

If for some reason you need to [cancel the ticket sale registration](/reference/uitpas.json/paths/~1ticket-sales~1%7BticketSaleId%7D/delete) you can do so using the `id` of the ticket sale in the response of the registration.

### Frequently asked questions

Having questions? Check out our [FAQ](/docs/faq)!
