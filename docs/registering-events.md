# Registering events

This guide will explain how to register an event as an UiTPAS event. This is required to be able to:

*   **Calculate UiTPAS prices (discounts)** for tickets to attend the event
*   **Register ticket sales** so the organizer of the event can receive compensation for the discount

> ##### Why do I need to register my event with UiTPAS?
>
> Some discounts or other advantages for UiTPAS passholders are **only available on specific events**, which is why every price calculation and ticket sale needs to be linked to a known event.

## Overview

To register an UiTPAS event, the following criteria have to be met:

1.  **The event needs to exist in UiTdatabank.** UiTdatabank is a central database of cultural and leisure activities in Flanders and Brussels, including events of organizers that work with UiTPAS.
2.  The event in UiTdatabank needs to have **a base price**.
3.  The event in UiTdatabank has to be **linked to a known UiTPAS organizer**.

When these criteria are met, the event automatically becomes known in UiTPAS by its UiTdatabank event id.

You can register your event in UiTdatabank in two ways: programmatically via **UiTdatabank's Entry API**, or manually via **UiTdatabank's user interface** (useful for testing for example).

## Entry API

To learn how to import your event programmatically through UiTdatabank's Entry API, [consult the UiTdatabank documentation for event imports](https://documentatie.uitdatabank.be/content/json-ld-crud-api/latest/events.html). (This documentation will be moved to this new documentation portal at a later stage.)

You can register your event with all required info in **a single request** to Entry API.

### Example request

```http
POST /imports/events/ HTTP/1.1
Host: https://io-test.uitdatabank.be
Authorization: Bearer YOUR_USER_OR_CLIENT_ACCESS_TOKEN
Content-Type: application/json

{
  "mainLanguage": "nl",
  "name": {
    "nl": "Example UiTPAS event"
  },
  "calendarType": "single",
  "startDate": "2021-01-01T12:00:00+01:00",
  "endDate": "2021-01-01T16:00:00+01:00",
  "location": {
    "@id": "https://io-test.uitdatabank.be/place/8248e289-c986-4006-902f-b0616dcbcde7"
  },
  "terms": [
    {
      "id": "0.50.4.0.0"
    }
  ],
  "priceInfo": [
    {
      "category": "base",
      "name": {
        "nl": "Basistarief"
      },
      "price": 11,
      "priceCurrency": "EUR"
    }
  ],
  "organizer": {
    "@id": "https://io-test.uitdatabank.be/organizers/67f645d5-72f6-487d-8908-f0f64c5a63d0"
  }
}
```

If successful, you should receive a `200` response with a JSON body including the id of your newly created event:

```http
{
  "id": "100d4f49-d155-4f1c-83cb-064291606e60"
}
```

<!-- theme: success -->

> ##### Done!
>
> If you used an **UiTPAS organizer id** in the JSON of the event and specified a base price, **your event will automatically become an UiTPAS event**. There can be small delay between the event being created in UiTdatabank and being discovered by UiTPAS, but generally this takes less than a second.
>
> You can check that an event has become an UiTPAS event by [fetching it's JSON from UiTdatabank](https://documentatie.uitdatabank.be/content/json-ld/latest/events/event-detail.html) and checking that it has the `UiTPAS` label in its `labels` property.

### Required properties

Of the properties in the example above, the following are always mandatory in UiTdatabank in general:

*   `mainLanguage`: The language that your event is described in.
*   `name`: The name of your event.
*   `calendarType`: Specifies how often your event takes place, and based on this other properties like `startDate` and `endDate` may be required.
*   `location`: The location the event takes place at.
*   `terms`: Describes what categories your event belongs to. At least one event type is required.

For more info on mandatory properties, see [the Entry API documentation](https://documentatie.uitdatabank.be/content/json-ld-crud-api/latest/events.html).

Additionally, your event requires two more properties to qualify as an UiTPAS event:

*   `priceInfo` with a `base` price.
*   `organizer` with the id of an UiTPAS organizer that organizes the event. Usually your client or user access token is coupled to one or more organizer ids inside UiTPAS and you should use one of those.

### Authentication

> When creating an UiTdatabank event through Entry API, **you can use the same user access token or client access token that you use to communicate with the UiTPAS API**.

## Via the user interface

You can manually enter events in UiTdatabank by browsing to <https://www.uitdatabank.be>. This is helpful when you just need an event for testing purposes.

Registering an UiTPAS event in UiTdatabank's user interface takes just 3 steps!

### 1. Creating a new event

Start by logging in on UiTdatabank with an existing UiTID account, or create one for free if you don't have one yet. Afterward you can immediately start entering events.

You can find more info in [our helpdesk article](https://helpdesk.publiq.be/hc/nl/articles/360016331140-Hulp-bij-het-invoeren-van-een-evenement) creating events in UiTdatabank.

After you have created an event, **you need to specify some additional info to turn it into an UiTPAS event**.

### 2. Specifying a price

Edit the *Prijs* field of your event, and provide a *Basistarief* that is not 0 EUR. For example:

<!-- focus: false -->
![](../assets/images/uitdatabank-event-price.png)

### 3. Linking an UiTPAS organizer

Click the *Organisatie toevoegen* button. You can now look for the organizer of the event by typing its name in the input field that has appeared, and then select it from the list of possible options that appears. If multiple options appear, make sure to select the one that has a grey *UiTPAS* badge next to it.

<!-- focus: false -->
![](../assets/images/uitdatabank-event-organizer.png)

### 4. Done!

Shortly after you have selected an UiTPAS organizer, you should see a new *UiTPAS* field appear in the form that looks like this:

<!-- focus: false -->
![](../assets/images/uitdatabank-event-uitpas.png)

**If you see this field, your event is now registered as an UiTPAS event**, and you can optionally change the possible card systems that it belongs to if the UiTPAS organizer is active in multiple card systems.

You can now **use its id in UiTPAS API requests** that require an event id, for example to register a ticket sale for it. You can find this id in the URL of the event's detail or edit page on UiTdatabank.

<!-- focus: false -->
![](../assets/images/uitdatabank-event-id.png)
