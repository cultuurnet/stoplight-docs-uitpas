# Registering ticket sales for group passes

This mini-guide illustrates how to register UiTPAS discounted ticket sales for a group pass. It builds upon the [Registering ticket sales guide](/docs/registering-ticket-sales-group.md) which you need to read first to learn more about authentication, the work flow and registering events.

> ##### What is a group pass?
>
> A group pass is very similar to a regular passholder, but it is not bound to one specific person, but to an organisation for example. These passes can be used to buy multiple tickets for the same discounted price, instead of just one. It's also identified by an UiTPAS number. The number of tickets that can be registered at a specific UiTPAS tariff is usually preset when the group pass is issued.

This mini-guide hooks into the regular flow when an end-user entered the UiTPAS number of a group pass instead of a regular passholder.

### 1. Determine possible UiTPAS tariffs

Using the event id, the UiTPAS number and the regular price of your event, you can [request possible UiTPAS tariffs](/reference/uitpas.json/paths/~1tariffs/get).

Your application doesn't (have to) know if the given UiTPAS number is a regular passholder or a group pass.

Example request:

```http
GET /tariffs/?eventId=5a0967f9-cc06-4c3c-9206-30481a767434&uitpasNumber=0900010630813&regularPrice=10 HTTP/1.1
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
      "remaining": 50
    }
  ]
}
```

In this example the end-user can select only one possible UiTPAS discount, the social tariff. The difference with a regular passholder tariff however, is the `remaining` property, which is 50 in this case. This means you can register up to 50 tickets for this UiTPAS number, for the given event.

### 2. End-user selects the tariff and number of tickets

If the API response contained one or more UiTPAS tariffs, your website or application should present them to the end-user to select one. In case the `remaining` property is higher than 1, your application can allow to increase the number of tickets, for the same event, for the same UiTPAS number.

### 3. Register the ticket sales

After the end-user has selected an UiTPAS tariff, your website or application continues with its regular flow for completing the sale like payment (for the discounted price) etc.

When your regular flow successfully finishes, you need to [register the ticket sale](/reference/uitpas.json/paths/~1ticket-sales/post). If you don't register the ticket sale correctly, the organizer can not get reimbursed for the discount within the UiTPAS financial flow.

For every ticket sale, you need to include a `TicketSale` object in the ticket sale request.

For example, to register 3 ticketsales for group pass `0900010630813`:

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
    "uitpasNumber": "0900010630813"
  },
  {
    "regularPrice": 10,
    "tariff": {
      "id": "SOCIALTARIFF"
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900010630813"
  },
  {
    "regularPrice": 10,
    "tariff": {
      "id": "SOCIALTARIFF"
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900010630813"
  }    
]
```

Have a look at the [test dataset](/docs/test-dataset) for more sample passholders or events.

For more information about each property, see the documentation for the [POST /ticket-sales](/reference/uitpas.json/paths/~1ticket-sales/post) endpoint.

Example response:

```http
200 OK HTTP/1.1
Content-Type: application/json

[
  {
    "id": "499853",
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
    "id": "499854",
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
    "id": "499856",
    "tariff": {
      "id": "SOCIALTARIFF",
      "name": "Kansentarief",
      "price": 2,
      "type": "SOCIALTARIFF"
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900000672312"
  }    
]
```

> Note that the response contains an id for every registered ticket sale. **We advise you to store this id** in your application in case you need to cancel the ticket sale later.

<!-- theme: warning -->

> **If one of the ticket sales is invalid** (for example the chosen tariff is incorrect or expired), **none of the ticket sales will be registered**. You will instead get an error response with more details about the problem, and can then retry the registration without the incorrect ticket sales or ask the passholder to change the tickets and/or tariffs that they want.

### 4. Cancelling the ticket sale

If for some reason you need to [cancel the ticket sale registration](/reference/uitpas.json/paths/~1ticket-sales~1%7BticketSaleId%7D/delete) you can do so using the `id` of the ticket sale in the response of the registration.

### Frequently asked questions

Having questions? Check out our [FAQ](/docs/faq)!
