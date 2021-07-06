# Registering ticket sales

This guide illustrates how to register an UiTPAS discounted ticket sale so end users can receive their UiTPAS discount and organizers can be reimbursed within the UiTPAS financial flow.

You'll learn how to request possible UiTPAS tariffs, register the ticket sale and even cancel it if needed.

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

![](../assets/images/steps-ticketing-UiTPAS-visual.png)

### 1. UiTdatabank event

Every ticket sale in UiTPAS is coupled to an event in UiTdatabank, for example because some discounts are only applicable to some events. Every UiTPAS event has an organizer, for which you are registering this ticket sale.

You can either use an existing UiTdatabank event, or create one manually via UiTdatabank's UI, or import one programmatically through UiTdatabank's API.

To learn more about how to register your event in UiTdatabank and turn it into an UiTPAS event, read [our guide on  registering events](./registering-events.md).

<!-- theme: warning -->

> ##### Creating events for ticket sales
>
> If your event is not in UiTdatabank yet, you only need to enter it there once. You should not create a new event in UiTdatabank for every ticket sale!

### 2. User wants to buy a ticket

Some time *after* you have registered your event in UiTdatabank, a user on your website or application wants to buy a ticket to the event.

Your application then starts its typical flow of guiding the user through a checkout process.

### 3. Determine UiTPAS number of the user

At some point during the checkout process on your website or application (but **before a payment has happened**), you provide the user a way to enter their UiTPAS number if they have one.

### 4. Determine possible UiTPAS tariffs

Using the event id, the UiTPAS number and the regular price of your event, you can [request possible UiTPAS tariffs](/reference/UiTPAS.v2.json/paths/~1tariffs/get).

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
    "id": "SOCIALTARIFF",
    "name": "Kansentarief",
    "price": 1.5
  }
]
```

### 5. User selects a tariff (or none)

If the API response contained one or more UiTPAS tariffs, your website or application should present them to the user to select one (or none).

For example if all the discounted tariffs are based on one-time-use coupons, but the user does not wish to use any coupons after all, he/she should be able to not select one.

### 6. Register the ticket sale

After the user has selected an UiTPAS tariff, your website or application continues with its regular flow for completing the sale like payment (for the discounted price) etc.

When your regular flow successfully finishes, you need to [register the ticket sale](/reference/UiTPAS.v2.json/paths/~1ticket-sales/post). If you don't register the ticket sale correctly, the organizer can not get reimbursed for the discount within the UiTPAS financial flow.

> If the user had no UiTPAS tariffs, or did not select one, you do not need to register your ticket sale with UiTPAS.

For example:

```http
POST /events/YOUR_EVENT_ID/ticket-sales HTTP/1.1
Content-Type: application/json
Host: https://api.uitpas.be
Authorization: Bearer YOUR_ACCESS_TOKEN'

[
  {
    "uitpasNumber": "0560002524314",
    "tarrifId": "SOCIALTARIFF",
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

If for some reason you need to [cancel the ticket sale registration](/reference/UiTPAS.v2.json/paths/~1ticket-sales~1%7BticketSaleId%7D/delete) you can do so using the `id` of the ticket sale in the response of the registration.

## F.A.Q.

### Will the UiTPAS API be able to handle all of my requests? I'm worried about a realtime dependency on UiTPAS at check out.

Only a small percentage of your requests should be accessing the UiTPAS API, as you only show [UiTPAS tariffs](/reference/UiTPAS.v2.json/paths/~1tariffs/get) for those users who entered an UiTPAS number or have an UiTPAS number saved in their user profile. Numerous applications are already successfully accessing the UiTPAS API every day, which which has been running since 2012 with very low downtime.

### Can the regularPrice sent in /ticket-sales differ from the prices listed in the UiTdatabank event?

Yes, you can send any price, not only the prices listed in the UiTdatabank event.
We strongly advice to always update the price of the UiTdatabank event when the price changes, as this price will be listed publicly on websites such as [UiTinVlaanderen.be](http://UiTinVlaanderen.be). 
Keeping this price up-to-date also helps us fight fraud and makes it easier to audit price changes.

### Why can't I calculate the UiTPAS tariffs in my application?

The calculation of the correct UiTPAS tariff is dependent on many factors such as the availability of a social tariff for the passholder and settings within the UiTPAS region. These are checked in realtime by UiTPAS. You should never calculate the UiTPAS tariff yourself, because only UiTPAS can [give you the correct tariff](/reference/UiTPAS.v2.json/paths/~1tariffs/get) for a specific passholder at the current time.

### Can I get a list of discounts instead of a list of tariffs?

No, [/tariffs](/reference/UiTPAS.v2.json/paths/~1tariffs/get) will give you the discounted tariffs for a given price.
You shouldn't calculcate these discounts yourself because they can vary from a relative (-50%) or an absolute discount (-5 euro).

### My events are private and I don't want them to be public. How can I do this?

One of the biggest advantages of creating UiTdatabank events, is that your events will be available throughout thousand of local events calendars and websites such as [UiTinVlaanderen.be](http://UiTinVlaanderen.be).

If you don't want your events to be published in this way you [should set the audienceType](https://documentatie.uitdatabank.be/content/json-ld-crud-api/latest/events/event-audience.html) to "members" on your events.

```
"audience": {
  "audienceType": "members"
}
```

### When should I register the ticket sale?

Within UiTPAS, there are constraints such as the number of tickets that can be sold at a social tariff or how many times a coupon can be used.
That's why it makes sense to already [register the ticket sale](/reference/UiTPAS.v2.json/paths/~1ticket-sales/post) right before the payment.
Otherwise if you wait to register the ticket sale until after the passholder has paid, the chosen tariff might not be valid anymore.

If the passholder doesn't end up buying the ticket, you should [cancel the ticket sale registration](/reference/UiTPAS.v2.json/paths/~1ticket-sales~1%7BticketSaleId%7D/delete). 

### Can I get a list of all UiTPAS numbers that have a social tariff ('kansentarief')?

The calculation of the correct UiTPAS tariffs is dependent on many factors such as the availability of a social tariff for the passholder and settings within the UiTPAS region. These are checked in realtime by UiTPAS. That's why it's a bad idea to only use UiTPAS numbers without accessing the UiTPAS API.
