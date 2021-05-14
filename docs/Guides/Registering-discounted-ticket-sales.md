# Registering discounted ticket sales

This guide illustrates how to register an UiTPAS discounted ticket sale, from requesting possisble tariffs to registering the ticket sale and even canceling it if needed.

## Authentication

Before you can request UiTPAS tariffs or register ticket sales, you'll need **client credentials**, so you can access the UiTPAS API using a [Client Access Token](https://publiq.stoplight.io/docs/authentication/docs/Authentication-methods/Client-access-token.md)

## Workflow overview

![](../../assets/images/steps-ticketing-UiTPAS-visual.png)

### 1. UiTdatabank event

Every ticket sale in UiTPAS is coupled to an event in UiTdatabank, for example because some discounts are only applicable to some events. Every UiTPAS-event has an organizer, for which you are registering this ticketsale. 

You can either use an existing UiTdatabank event, or create one manually via UiTdatabank's UI, or import one programmatically through UiTdatabank's API.
  
To learn more about how to register your event in UiTdatabank and turn it into an UiTPAS event, read [our guide on  registering events](/docs/Guides/Registering-events.md).

<!-- theme: warning -->
> ##### Creating events for ticket sales
> If your event is not in UiTdatabank yet, you only need to enter it there once. You should not create a new event in UiTdatabank for every ticket sale!

### 2. User wants to buy a ticket

Some time _after_ you have registered your event in UiTdatabank, a user on your website or application wants to buy a ticket to the event.

Your application then starts its typical flow of guiding the user through a checkout process.

### 3. Determine UiTPAS number of the user

At some point during the checkout process on your website or application (but **before a payment has happened**), you provide the user a way to enter their UiTPAS number if they have one.

### 4. Determine possible discounted tariffs

Using the event id, the UiTPAS number and the regular price of your event, you can [request possible UiTPAS tariffs](https://publiq.stoplight.io/docs/uitpas/reference/UiTPAS.v2.json/paths/~1tariffs/get).

Example request:

```http
GET /tariffs/?eventId=EVENT_ID&uitpasNumber=UITPAS_NUMBER&regularPrice=10 HTTP/1.1
Host: https://api.uitpas.be
Authorization: Bearer YOUR_CLIENT_ACCESS_TOKEN'
```

Example response:

```http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id": "KANSENTARIEF",
    "name": "Kansentarief",
    "price": 1.5
  }
]
```

### 5. User selects a discounted tariff (or none)

If the API response contained one or more discounted tariffs, your website or application should present them to the user to select one (or none).

For example if all the discounted tariffs are based on one-time-use coupons, but the user does not wish to use any coupons after all, he/she should be able to not select one.

### 6. Register the ticket sale

After the user has selected a discounted tariff, your website or application continues with its regular flow for completing the sale like payment (for the discounted price) etc.

When your regular flow successfully finishes, you need to [register the ticket sale](/docs/uitpas/reference/UiTPAS.v2.json/paths/~1ticket-sales/post). If you don't register the ticketsale correctly, the organizer can not get reimbursed for the discount within the UiTPAS financial flow.

> If the user had no discounted tariffs, or did not select one, you do not need to register your ticket sale with UiTPAS.

For example:


```http
POST /events/YOUR_EVENT_ID/ticket-sales HTTP/1.1
Content-Type: application/json
Host: https://api.uitpas.be
Authorization: Bearer YOUR_ACCESS_TOKEN'

[
  {
    "uitpasNumber": "0560002524314",
    "tarrifId": "KANSENTARIEF",
    "eventId": "31e926e2-a35f-11eb-bcbc-0242ac130002",
    "regularPrice": 10,
    "regularPriceLabel": "Base tariff"
  }
]
```

As you can see, you can include multiple ticket sale registrations at once. This can be helpful when you want to provide your users a way to buy multiple tickets at once.

Example response:

```http
200 OK HTTP/1.1
Content-Type: application/json

[
  {
    "id": 21345,
    "tariff": {
      "id": "COUPON1234",
      "name": "Cultuurbon 6 euro",
      "price": 1.5
    },
    "uitpasNumber": "0560002524314",
    "eventId": "31e926e2-a35f-11eb-bcbc-0242ac130002"
  }
]
```

> Note that the response contains an id for every registered ticket sale. **We advise you to store this id** in your application or website for future reference and in case you need to cancel the ticket sale later.

<!-- theme: warning -->

> **If one of the ticket sales is invalid** (for example the chosen tariff is incorrect or expired), **none of the ticket sales will be registered**. You will instead get an error response with more details about the problem, and can then retry the registration without the incorrect ticket sales or ask the end user to change the tickets and/or tariffs that they want.

### 7. Cancelling the ticket sale

If for some reason you need to [cancel the ticket sale registration](/docs/uitpas/reference/UiTPAS.v2.json/paths/~1ticket-sales~1%7BticketSaleId%7D/delete) you can do so using the `id` of the ticket sale in the response of the registration.

## F.A.Q.

### Can I get a list of all UiTPAS numbers that have a sociall tariff ('kansentarief')?

### Can I get a list of discounts instead of a list of tariffs?

