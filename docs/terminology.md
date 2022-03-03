# Terminology

On this page you can quickly get a grasp of common UiTPAS terminology and concepts, referenced throughout the API documentation.

## Passholder

One who holds an UiTPAS, the end-user of an UiTPAS, identified by an UiTPAS number.

## Group pass

Similar to a passholder, but issued to a group of people in specific circumstances, identified by an UiTPAS number.

## UiTPAS Event

Certain actions within UiTPAS are linked to specific events, for example when [registering a ticket sale](./registering-ticket-sales.md) at a discounted UiTPAS tariff.

Those events need to exist in [UiTdatabank](https://docs.publiq.be/docs/uitdatabank). UiTdatabank is a central database of cultural and leisure activities in Flanders and Brussels. [To register an UiTPAS event](./registering-events.md), the event in UiTdatabank needs to have a base price and has to be linked to a known UiTPAS organizer.

## UiTPAS Organizer

The organizer of an UiTPAS event. To pay for the social tariff for people in need, the organizer is partly reimbursed by the local government. That's why it's important to always create UiTPAS events with the correct UiTPAS organizer.

Just like events, organizers are saved in [UiTdatabank](https://docs.publiq.be/docs/uitdatabank).

## UiTPAS Tariffs

UiTPAS [tariffs](/reference/uitpas.json/paths/~1tariffs/get) are the discounted prices a specific passholder may receive for a specific UiTPAS event. For example, a passholder may receive a social tariff or may have a redeemable coupon.

## Social tariff ('kansentarief')

Passholders in need can receive a social tariff ('kansentarief') on their UiTPAS.
This social tariff is given by the local government based on local poverty parameters.

Those discounts are paid for via a third-party payer system in which the organizer of the event, the local government and the passholder each pay a percentage of the ticket price. Payments are managed via a financial reporting flow within UiTPAS.

## Coupon

An UiTPAS coupon is a discount that is made available to a subset of passholders.
Based on certain parameters such as the residence of the passholder or a specific UiTPAS event, this coupon is automatically made available to the passholder.

For example: the City of Hasselt awards a 5 euro coupon to all passholders of Hasselt.
This coupon will automatically be made available to all inhabitants of Hasselt.
When the UiTPAS [tariffs](/reference/uitpas.json/paths/~1tariffs/get) for an inhabitant of Hasselt are checked, this coupon will show up as an option and can then be redeemed.

## Ticket sale

When you register an UiTPAS discounted tariff for a specific passholder on a specific UiTPAS event, we call this a [ticket sale](/reference/uitpas.json/paths/~1ticket-sales/post).

## Check-in

A passholder can save an UiTPAS point by checking in to an UiTPAS event.
An UiTPAS point is earned for participating in a specific event, mostly by holding the card to a check-in device located at the organizer of the event.

## Rewards

After saving UiTPAS points, a passholder can trade them for [rewards](https://www.uitpas.be/voordelen-zoeken#/voordelen) provided by local UiTPAS organizers.

Some rewards can only be traded at the event of the organizer (material rewards like a goodie bag). Other rewards can also be traded online via [UiTPAS.be](http://uitpas.be) (digital rewards such as an e-ticket).
