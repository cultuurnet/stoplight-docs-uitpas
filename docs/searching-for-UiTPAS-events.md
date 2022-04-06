# Searching for UiTPAS events

This mini-guide will explain how to search for UiTPAS events using the [Search API](https://documentatie.uitdatabank.be/content/search_api_3/latest/searching.html).

### Authentication

You can make use of the Search API with [client identification](https://docs.publiq.be/docs/authentication/ZG9jOjExODE5NDY5-client-identification).

**These APIs only require you to specify the client id of your existing UiTPAS integration** for customization and technical support purposes.

### Searching for UiTPAS events of one specific organizer

You can search for all the UiTPAS events of one UiTPAS organizer by using the organizerId of the UiTPAS organizer.

```json http
{
  url: 'https://search-test.uitdatabank.be/events/',
  method: "GET",
  query: {
    "clientId": "YOUR_TEST_ENV_CLIENT_ID",
    "uitpas":true,
    "organizerId":"0ce87cbc-9299-4528-8d35-92225dc9489f"
  }
}
```

### Searching for UiTPAS events of multiple UiTPAS organizers

You can search for the UiTPAS events of multiple UiTPAS organizers by using the q paramter.

```json http
{
  url: 'https://search-test.uitdatabank.be/events/',
  method: "GET",
  query: {
    "clientId": "YOUR_TEST_ENV_CLIENT_ID",
    "uitpas":true,
    "q":"organizer.id:<UUID> OR organizer.id:<UUID>"
  }
}
```