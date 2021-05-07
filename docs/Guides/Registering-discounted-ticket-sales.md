# Registering discounted ticket sales

This guide illustrates how to register an UiTPAS discounted ticket sale, from requesting possisble tariffs to registering the ticket sale and even canceling it if needed.

<!-- theme: warning -->

> Carefully read the prerequisites below before you start

## Prerequisites

Before you can request UiTPAS tariffs or register ticket sales, you'll need:

**Client credentials**, so you can access the UiTPAS API using a [Client Access Token](https://publiq.stoplight.io/docs/authentication/docs/Authentication-methods/Client-access-token.md)

**An UiTDatabank event id** organized by an UiTPAS organizer. There are multiple ways to get such an event id:

  * The event may already exist in UiTDatabank
  * You could create the event in the UiTdatabank manually 
  * You can import the event programmatically through UiTdatabank's Entry API

To learn more about how to register your event in UiTdatabank and turn it into an UiTPAS event, read [our guide on  registering events](/docs/Guides/Registering-events.md).


## Workflow overview

![Auth Diagram](https://acc.uitid.be/api/uitpas-ticketsale-flow.png)

1. A typical workflow starts with a user that wants to buy a ticket. This can work in various ways, specific to your application, but we start this flow when the user expresses his need to apply an UiTPAS discount.

2. The next step your application needs to take is determining the UiTPAS event id and price. Your application can do this in various ways, see prerequisites. You also need to determine the price for the ticket the user wants to buy if you haven't already done so. 

> It is important that the regular price the user is offered is also available in at least one of the price categories of the UiTdatabank event.

3. Next, your application need to determine the UiTPAS number of the user. Usually simply by requesting user input, but other means like storing the UiTPAS number in a user profile is also possible.

4. Using the event id, the UiTPAS number and the regular price, you can [request possible UiTPAS tariffs](/docs/uitpas/reference/UiTPAS.v2.json/paths/~1tariffs/get).

Example request:

```http
GET /events/YOUR_EVENT_ID/tariffs/USER_UITPAS_NUMBER?regularPrice=10 HTTP/1.1
Host: https://api.uitpas.be
Authorization: Bearer YOUR_ACCESS_TOKEN'
```

Example response:

```http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "uitpasTariffId": "KANSENTARIEF",
    "name": "Kansentarief",
    "tariff": 1.5
  }
]
```

5. If the tariffs request returns multiple tariffs, your application needs to show them to the user so he/she can select the appropriate tariff. If there's only one tariff, your application can go straight to step 6.

Example response with multiple tariffs:

```http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id": "KANSENTARIEF",
    "name": "Kansentarief",
    "tariff": 1.5
  },
  {
    "id": "COUPON1234",
    "name": "Cultuurbon 6 euro",
    "tariff": 4
  },
  {
    "id": "COUPON2345",
    "name": "2 euro korting op een specifiek event",
    "tariff": 8
  }
]
```


6. Your application shows the (auto)selected tariff and ask the user for confirmation. 

7. Your application can now continue its regular flow, like asking the user for payment (of the discounted tariff).

8. When your regular flow successfully finishes, you need to [register the ticket sale](/docs/uitpas/reference/UiTPAS.v2.json/paths/~1ticketSales/post), again using the event id, the UiTPAS number of the user, the regular price, combined with the selected tariffId of the selected tariff.


```http
POST /events/YOUR_EVENT_ID/ticketSales HTTP/1.1
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

As you can see, you can include multiple ticket sale registrations at once.

Example response:
```http
HTTP/1.1 200 OK
Content-Type: application/json

[
  {
    "id": 21345,
    "tariff": {
      "id": "COUPON1234",
      "name": "Cultuurbon 6 euro",
      "price": 4
    },
    "uitpasNumber": "0560002524314",
    "eventId": "31e926e2-a35f-11eb-bcbc-0242ac130002"
  },
  {
    "id": 21346,
    "tariff": {
      "id": "STRUCTURALDISCOUNT",
      "name": "Kansentarief",
      "price": 1.5
    },
    "uitpasNumber": "0940002524123",
    "eventId": "31e926e2-a35f-11eb-bcbc-0242ac130002"
  }
]
```

In case the ticketsale registration fails, the status property will contain more information. 

If for some reason you need to [cancel the ticket sale registration](/docs/uitpas/reference/UiTPAS.v2.json/paths/~1ticketSales~1%7BticketSaleId%7D/delete) you can do so using the `id` of the ticketSale from this response.

## F.A.Q.

### Can I get a list of all UiTPAS numbers that have a structural discount ('kansentarief')?

### Can I get a list of discounts instead of a list of tariffs?

