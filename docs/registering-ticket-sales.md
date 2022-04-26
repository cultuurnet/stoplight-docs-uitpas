# Registering ticket sales

This guide illustrates how to register an UiTPAS discounted ticket sale so passholders can receive their UiTPAS discount and organizers can be reimbursed within the UiTPAS financial flow.

You'll learn how to request available UiTPAS tariffs for an event and passholder, register the ticket sale and even cancel it if needed.

The whole flow consists of approximately two to four API calls depending on your situation.

> ##### Why do I need to register my ticket sales in UiTPAS?
>
> If you sell a ticket for an event to an UiTPAS passholder **with an UiTPAS discount**, the ticket sale needs to be registered in UiTPAS so **the organizer can be reimbursed for the discount**.
>
> Regular ticket sales, or ticket sales to UiTPAS passholders without a discount, do not need to be registered in UiTPAS as there is no reimbursement needed then.

## Authentication

Before you can request UiTPAS tariffs or register ticket sales, you'll need **client credentials**, so you can access the UiTPAS API using a [Client Access Token](https://publiq.stoplight.io/docs/authentication/docs/client-access-token.md) or [User access Token](https://publiq.stoplight.io/docs/authentication/docs/user-access-token.md).

To decide what kind of token to use, see the [overview of token types](https://publiq.stoplight.io/docs/authentication/docs/methods.md).

## Workflow overview

<!-- focus: false -->
![](../assets/images/steps-ticketing-UiTPAS-visual.png)

### 1. UiTdatabank event

Every ticket sale in UiTPAS is coupled to an event in UiTdatabank, for example because some discounts are only applicable to some events. Every UiTPAS event has an organizer, for which you are registering this ticket sale.

You can either use an existing UiTdatabank event, or create one manually via UiTdatabank's UI, or import one programmatically through UiTdatabank's API.

To learn more about how to register your event in UiTdatabank and turn it into an UiTPAS event, read [our guide on  registering events](./registering-events.md).

<!-- theme: warning -->

> ##### Creating events for ticket sales
>
> If your event is not in UiTdatabank yet, you only need to enter it there once. You should not create a new event in UiTdatabank for every ticket sale!

### 2. Passholder wants to buy a ticket

Some time *after* you have registered your event in UiTdatabank, a passholder on your website or application wants to buy a ticket to the event.

Your application then starts its typical flow of guiding the passholder through a checkout process.

### 3. Determine UiTPAS number of the passholder

At some point during the checkout process on your website or application (but **before a payment has happened**), you provide the passholder a way to enter their UiTPAS number if they have one.

Optionally the UiTPAS number can already be verified at this stage by [retrieving pass](/reference/uitpas.json/paths/~1passes/%7BuitpasNumber%7D/get) information. This endpoint also provides basic information like the passholder's points, postalCode and if the social tariff is expired. 


### 4. Determine available UiTPAS tariffs

Using the event id, the UiTPAS number and the regular price of your event, you can [request available UiTPAS tariffs](/reference/uitpas.json/paths/~1tariffs/get).

Example request:

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

In this example the passholder can select two possible UiTPAS discounts. The social tariff, and a coupon. Both tariffs are valid for one ticket for this event for this passholder.

> ##### remaining tickets at a tariff
>
> For regular passholders, the `remaining` value is always 1. Please refer to [registering ticket sales for group passes](/docs/registering-ticket-sales-group) for more information on group pass ticket sales.

### 5. Passholder selects a tariff

If the API response contained one or more UiTPAS tariffs, your website or application should present them to the passholder to select one (or none). Since some discounts are only usable once, it's important that passholders can choose when to use a specific discount.

For example if all the discounted tariffs are based on one-time-use coupons, but the passholder does not wish to use any coupons after all, he/she should be able to not select one.

> ##### type
>
> Tariffs can be of different types, e.g. `SOCIALTARIFF` or `COUPON`.
> *If* the passholder is entitled to a social tariff, only one such tariff will be available. If your application does not wish to support the use of coupon tariffs, it could auto-select the tariff with type `SOCIALLTARIFF` when available, without asking the pasholder.

### 6. Register the ticket sale

After the passholder has selected an UiTPAS tariff, the [ticket sale(s) must be registered](/reference/uitpas.json/paths/~1ticket-sales/post). Only after this registration step, your application can be sure that the UiTPAS discounted price can be granted. If you don't register the ticket sale correctly, the organizer can not get reimbursed for the discount within the UiTPAS financial flow.

> If the passholder had no UiTPAS tariffs, or did not select one, you do not need to register your ticket sale with UiTPAS.

For example:

```http
POST /ticket-sales HTTP/1.1
Content-Type: application/json
Host: https://api.uitpas.be
Authorization: Bearer YOUR_ACCESS_TOKEN'

[
  {
    "regularPrice": 10,
    "regularPriceLabel": "Base tariff", # Optional
    "tariff": {
      "id": "SOCIALTARIFF"
    },
    "eventId": "5a0967f9-cc06-4c3c-9206-30481a767434",
    "uitpasNumber": "0900000672312"
  }
]
```

Have a look at the [test dataset](/docs/test-dataset) for more sample passholders or events.

As you can see, you can also include multiple ticket sale registrations at once. Read more about this in the [registering multiple ticket sales at once](/docs/registering-ticket-sales-multiple) mini guide.

This can be helpful when you want to provide your passholders a way to buy multiple tickets at once.

For more information about each property, see the documentation for the [POST /ticket-sales](/reference/uitpas.json/paths/~1ticket-sales/post) endpoint.

Example response:

```http
200 OK HTTP/1.1
Content-Type: application/json

[
  {
    "id": "499853",
    "regularPriceLabel": "Base tariff",
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

Note that depending on your client's permissions, the response can also contain a full passholder object next to the uitpasNumber. In a regular online ticket sale flow, this is usually not needed.

> Note that the response contains an id for every registered ticket sale. **We advise you to store this id** in your application in case you need to cancel the ticket sale later.

<!-- theme: warning -->

> **If one of the ticket sales is invalid** (for example the chosen tariff is incorrect or expired), **none of the ticket sales will be registered**. You will instead get an error response with more details about the problem, and can then retry the registration without the incorrect ticket sales or ask the passholder to change the tickets and/or tariffs that they want.

After registering the ticket sale, your website or application should continue with its regular flow for completing the sale like payment (for the discounted price) etc.

### 7. Cancelling the ticket sale

If for some reason you need to [cancel the ticket sale registration](/reference/uitpas.json/paths/~1ticket-sales~1%7BticketSaleId%7D/delete) you can do so using the `id` of the ticket sale in the response of the registration.

### Frequently asked questions

Having questions? Check out our [FAQ](/docs/faq)!
