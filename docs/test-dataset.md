# UiTPAS test dataset

## Organizers

| Name   |      ID      |
|----------|:-------------:|
| \[TEST] UiTPAS Organisatie (Regio Gent + Paspartoe) |  `0ce87cbc-9299-4528-8d35-92225dc9489f` |

Your test client should have permission to this organizer. You can double-check which organizer you have permission to by using [GET /permissions](/reference/UiTPAS.v2.json/paths/~1permissions/get)

```http
GET /permissions HTTP/1.1
Host: https://api.uitpas.be
Authorization: Bearer YOUR_ACCESS_TOKEN'
```

    [
      {
        "organizer": {
          "id": "0ce87cbc-9299-4528-8d35-92225dc9489f",
          "name": "[TEST] UiTPAS Organisatie (Regio Gent + Paspartoe)"
        }
      }
    ]

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
| `0900000008301` | no | 9000 | no | active |
| `0900000672312` | yes (Gent) | 9090 | no | active |
| `0900010888817` | expired |  9000 | 1 coupon (Gent) | active |
| `0900010261411` | yes (Gent) |  9090 | no | active |
| `0900005183117` | yes (Gent) |  9000 | no | blocked |
| `0900000999715` | expired |  1000 | no | active |
| `1000000301704` | no |  9000 | no | active |
| `0900000007402` | yes (Paspartoe) |  9000 | 2 coupons (Paspartoe) | active |
| `0900011091916` | yes |  n/a | n/a | unregistered |
| `0900000008905` | no |  n/a | n/a | unregistered |

## Group passes

| UitpasNumber      | Social tariff | Coupons | Card status |
|----------|:-------------:|:-------------:|:-------------:|
| `0900010630813` | yes | no | active |
