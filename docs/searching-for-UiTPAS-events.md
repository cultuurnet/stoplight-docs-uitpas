# Searching for UiTPAS events

This mini-guide will explain how to search for UiTPAS events using the [Search API](https://documentatie.uitdatabank.be/content/search_api_3/latest/searching.html).

> ##### Getting the organizerIds
>
> The Search API allows you to search in all UiTPAS events.
> Mostly it only makes sense to search for events of the UiTPAS organizer the current user or client has access to.
>
> That's why you should first check which organizers you currently have UiTPAS permissions for using the [GET /permissions](https://docs.publiq.be/docs/uitpas/b3A6NDM0MjM5NzY-get-permissions) endpoint.
>
> If you don't perform this check, you risk trying to perform UiTPAS actions for organizers you don't have permissions for, which will fail.

### Authentication

You can make use of the Search API with [client identification](https://docs.publiq.be/docs/authentication/ZG9jOjExODE5NDY5-client-identification).

**These APIs only require you to specify the client id of your existing UiTPAS integration.**

### Searching for UiTPAS events of one specific organizer

You can search for all the UiTPAS events of one UiTPAS organizer by using the organizerId of the UiTPAS organizer.

```json http
{
  url: 'https://search-test.uitdatabank.be/events/',
  method: "GET",
  query: {
    "clientId": "YOUR_TEST_ENV_CLIENT_ID",
        "organizerId":"0ce87cbc-9299-4528-8d35-92225dc9489f",
    "uitpas":true,
    "embed":true
  }
}
```

### Searching for UiTPAS events of multiple UiTPAS organizers

You can search for the UiTPAS events of multiple UiTPAS organizers by using the q parameter.

```json http
{
  url: 'https://search-test.uitdatabank.be/events/',
  method: "GET",
  query: {
    "clientId": "YOUR_TEST_ENV_CLIENT_ID",
     "q":"organizer.id:<UUID> OR organizer.id:<UUID>"
     "uitpas":true,
     "embed":true
  }
}
```
