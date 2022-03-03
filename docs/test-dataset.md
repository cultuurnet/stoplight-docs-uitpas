# UiTPAS test dataset

## Organizers

| Name   |      ID      |
|----------|:-------------:|
| \[TEST] UiTPAS Organisatie (Regio Gent + Paspartoe) |  `0ce87cbc-9299-4528-8d35-92225dc9489f` |

Your test client should have permission to this organizer. You can double-check which organizer you have permission to by using [GET /permissions](/reference/uitpas.json/paths/~1permissions/get)

```http
GET /permissions HTTP/1.1
Host: https://api.uitpas.be
Authorization: Bearer YOUR_ACCESS_TOKEN'
```

Example response:

```json
[
  {
    "organizer": {
      "id": "0ce87cbc-9299-4528-8d35-92225dc9489f",
      "name": "[TEST] UiTPAS Organisatie (Regio Gent + Paspartoe)"
    }
  }
]
```

You will need the organizer id when registering events.

## Events

If you want to test ticket-sales without registering your own event, you can use these existing events:

| Name   |      ID      | Details |
|----------|:-------------:|:-------------:|
| Permanente UiTPAS Test Festiviteit | `5a0967f9-cc06-4c3c-9206-30481a767434` | Allows 100 ticket sales per passholder per week |

## Passholders

When testing ticket sale requests, you'll need an UitpasNumber of a passholder. The table below contains some samples in different flavors:

| UitpasNumber      | Social tariff | Postal code | Coupons | Card status |
|----------|:-------------:|:-------------:|:-------------:|:-------------:|
| `0900000095902` | no | 9000 | no | active |
| `0900000067513` | yes (Gent) | 9090 | no | active |
| `0900000031618` | expired |  9000 | 1 coupon (Gent) | active |
| `0900000036112` | yes (Gent) |  9090 | no | active |
| `0900000002312` | yes (Gent) |  9000 | no | blocked |
| `0900000058918` | expired |  1000 | no | active |
| `1000000520006` | no |  9000 | no | active |
| `0900000093204` | yes (Paspartoe) |  9000 | 2 coupons (Paspartoe) | active |
| `0900000074519` | yes |  n/a | n/a | unregistered |
| `0900000075706` | no |  n/a | n/a | unregistered |

## Group passes

| UitpasNumber      | Social tariff | Coupons | Card status |
|----------|:-------------:|:-------------:|:-------------:|
| `0900000045410` | yes | no | active |
