# A hub for managing and distributing your addresses

> tl;dr Idea for a web app that allows you to login and save your address. Then have the ability to connect your addresses across multiple sites in a centralized fashion. Think facebook connect for addresses.

The web is a vibrant and thriving place. While there may be a ton of advancement happening every day to make our lives easier, some of the things we do everyday are still traditional and repetitive. One of those things is managing and distributing our physical addresses. How can we make managing addresses easier across the entire web? This document is a specification of how there could be a service to help rectify this very issue.

## Problems to solve

The first major problem is that every site stores your address on it's own servers. That means it's decentralized. Let's say you move where you live, you can't just update your address in one place and have this change for every site.

The second major problem is tied to the first. When you place an order online, if you enter the wrong address (which would happen less frequently with this service) you have an allotted amount of time before that order ships, I call this time the `buffer time`. This service aims to help manage this with a high level of efficiency and detail. Currently the only way to change an address on most sites is to get in touch with a person directly, and have them do some manual process to update an address.

The third major problem is privacy. With this app you'll be able to list who has had access to an address, when, what address, and how frequently. You can even block other users, apps, or sites, from requesting your address.

## Contents

This document is broken up into a couple of parts.

* Membership Schema
  * The membership schema is a list of the different types of accounts there are and the different capabilities and privileges they have.
* Mockups
  * Mockups are examples of webpages and the flow from one to the next.
* API
  * The API describes a bit about how some of the data comes into the app technically.
* Dashboard
  * The dashboard demonstrates the type of information that the user would have on their end.

## Membership Schema

There are three different types of memberships `Site`, `Apps`, `Users`.

* `Site` (ex. `jcrew.com`)
  * is an `App` with a verified url
  * can contain return address `Address`
  * can request address from `User` directly
  * can request address from `User` via another `User`
* `App` (ex. `fanmail`)
  * is an `App` without verified url
  * can contain return address `Address`
  * can request address from `User` via another `User`
  * can request address from `User` on behalf of another `User` (the requesting `User` will never see the address or send the mail themselves)
* `User` (ex. `thomasreggi`)
  * can have many `Apps`.
  * can request address from `User` directly
  * can contain return address `Address`
  * can contain `Address`

## Mockups

Below are mockups that describe different webpages and the flow from one page to the next. Each square is a figure, they move from left to right, you might need to scroll to the right to see some of them.

### Mail app site and dashboard

* Figure 1. View Homeapge
* Figure 2. Login to the app, if not already logged in.
* Figure 3. View Dashbaord

```
+---------------------------+   +---------------------------+   +---------------------------+
|                           |   |Mail App: Login            |   |Mail App: Dashboard        |
|                           |   +---------------------------+   +---------------------------+
|        MAIL APP           |   |                           |   |                           |
|                           |   | Username:                 |   |  - Addresses              |
|    [signup] [Login]       |   | +-----------------------+ |   |    * new / update         |
|                           |   | +-----------------------+ |   |  - Manage Requests        |
+---------------------------+==>| Password:                 |==>|                           |
| * organize your addresses |   | +-----------------------+ |   |                           |
|   across all sites        |   | +-----------------------+ |   |                           |
| * update all your sites   |   |                           |   |                           |
|   at once                 |   |      [sign-up] or [login] |   |                           |
| * easily request address  |   |                           |   |                           |
+---------------------------+   +---------------------------+   +---------------------------+
```

### User request: Recipient

* Figure 1. User receiving email message to provide access to their address
* Figure 2. Login to the app, if not already logged in.
* Figure 3. Approve and select address or deny request.

In this case this is a direct user-to-user connection where one user is requesting another's address.

```
+---------------------------+   +---------------------------+   +---------------------------+
|gmail.com                  |   |Mail App: Login            |   |Mail App: Authorize        |
+---------------------------+   +---------------------------+   +---------------------------+
|    |                      |   |                           |   |                           |
|    | user thomasreggi     |   | Username:                 |   |    user thomasreggi       |
|    | would like to        |   | +-----------------------+ |   |    would like to          |
|    | access your address  |   | +-----------------------+ |   |    access your address    |
|    |                      |==>| Password:                 |==>|                           |==> ?
|    | [login]              |   | +-----------------------+ |   |  * [approve]              |
|    |                      |   | +-----------------------+ |   |  - [address dropdown]     |
|    |                      |   |                           |   |  - [auto-accept requests] |
|    |                      |   |      [sign-up] or [login] |   |  * [decline]              |
|    |                      |   |                           |   |                           |
+----+----------------------+   +---------------------------+   +---------------------------+
```

### User request: Potential Sender

* Figure 1. Request addresses from user email (will be asked to signup / login)
* Figure 2. Shows list of Pending Requests.
* These Events lead to `User request: Recipient`

```
+---------------------------+   +---------------------------+
|Mail App: Request Address  |   |Mail App: Requests         |
+---------------------------+   +---------------------------+
|                           |   |                           |
|    Email:[************]   |   | Email | Status | Addr     |
|    --or--                 |   | +-----|--------|--------+ |
|    Username:[*********]   |   | +-----|--------|--------+ |
|    --or--                 |==>| +-----|--------|--------+ |
|    [Upload User Sheet]    |   | +-----|--------|--------+ |
|                           |   | +-----|--------|--------+ |
|    [submit]               |   |                           |
|                           |   |                           |
|                           |   |                           |
+---------------------------+   +---------------------------+
```

### App Login: E-Commerce Checkout

* Figure 1. User is checking out on a website and needs to connect address, signs in.
* Figure 2. Login to the app, if not already logged in.
* Figure 3. Approve and select address or deny request.

This is a site requesting a users address. A site is is a property of a user account. Sites are just like apps. Sites are verified. Sites can only request address they don't contain their own addresses. This means that registered sites like `crateandbarrel.com`, `amazon.com`, `jcrew.com` can register and verify their sites and use their real names.

```
+---------------------------+   +---------------------------+   +---------------------------+
|                           |   |Mail App: Login            |   |Mail App: Authorize        |
| jcrew.com Checkout        |   +---------------------------+   +---------------------------+
| +-----------------------+ |   |                           |   |                           |
|                           |   | Username:                 |   |    site jcrew.com         |
| item 1 ............ 10.00 |   | +-----------------------+ |   |    would like to          |
| item 2 ............ 10.00 |   | +-----------------------+ |   |    access your address    |
|                           |==>| Password:                 |==>|                           |==> {Redirect back to cart}
| Connect your address      |   | +-----------------------+ |   |  * [approve]              |
|                           |   | +-----------------------+ |   |  - [address dropdown]     |
| [sign in]                 |   |                           |   |  - [auto-accept requests] |
|                           |   |      [sign-up] or [login] |   |  * [decline]              |
|                           |   |                           |   |                           |
+---------------------------+   +---------------------------+   +---------------------------+
```

### App Login: E-Commerce Platform Checkout

* Figure 1. User is checking out on a website and needs to connect address, signs in.
* Figure 2. Login to the app, if not already logged in.
* Figure 3. Approve and select address or deny request.

Some platforms out there connect two users together (`etsy.com`, `ebay.com`, `amazon.com`). For instance when you purchase something from `ebay.com` you give your address to the ebay platform, and a user on ebay receives the address and ships the product with it. In this case `ebay.com` would have to allow the seller to register their account and connect the two users.

```
+---------------------------+   +---------------------------+   +---------------------------+
|                           |   |Mail App: Login            |   |Mail App: Authorize        |
| Ebay.com Checkout         |   +---------------------------+   +---------------------------+
| +-----------------------+ |   |                           |   |    user thomasreggi       |
|                           |   | Username:                 |   |    via site amazon.com    |
| item 1 ............. 3.00 |   | +-----------------------+ |   |    would like to          |
|                           |   | +-----------------------+ |   |    access your address    |
|                           |==>| Password:                 |==>|                           |==> {Redirect back to cart}
| Connect your address      |   | +-----------------------+ |   |  * [approve]              |
|                           |   | +-----------------------+ |   |  - [address dropdown]     |
| [sign in]                 |   |                           |   |  - [auto-accept requests] |
|                           |   |      [sign-up] or [login] |   |  * [decline]              |
|                           |   |                           |   |                           |
+---------------------------+   +---------------------------+   +---------------------------+
```

### Send mail

This is an idea for an app called `Send Mail` an app that connects to the `Mail App API` could be made by a third-party or the same creator as `Mail App`.

* Figure 1. The landing page to the app with login button.
* Figure 2. Login to the app, if not already logged in.
* Figure 3. Approve or decline the app that will send mail on your behalf.
* Figure 4. Craft a message, enter the email address of the recipient, or the username.
  * This would lead the recipient to [App's request address on behalf of user]
* Figure 5. View pending messages.
  * If the user doesn't have an account they will be prompted to make an account.
  * If the user has auto-approved messages from you as a user, or auto-accepts mail from this app it will be auto accepted.
  * If the status of a message is approved then the senders address is in the system and mail can be sent. Pay to send it.

This app would allow a user to send another user mail without the sender ever getting hold of the recipients address. I thought of this app in tandem with a physical business. The idea being that the creator of this app would be the proxy between the two users. They would be a third party that does the mailing for a customer. All that's really needed to pull this off is a list of all letters to mail for the day, a box of [envelopes with two plastic windows](http://i.imgur.com/6wFDumi.jpg) and a ream of 8.5"x11" paper. Using the api the creator can export a all the PDF letters for a day, fold the, stamp the envelopes, and mail them. In this case someone would need to pay the app creator to mail the letter. If under some promotion this may be able to be to funded by the Fan's recipient in bulk. Say Neil Degrasse Tyson or Bill Nye was running a promotion (ideally taking questions about scientific inquiries) then all mail to him would be paid for by a third party and not the sender.

There are also less involved ways of sending physical mail, there are many api's that already exist that allow you to do it.

This app could also be harnessed to send people in your contacts (that you have the email of).

```
+---------------------------+   +---------------------------+   +---------------------------+   +---------------------------+   +---------------------------+
|                           |   |Mail App: Login            |   |Mail App: Authorize        |   |SENDMAIL: Craft Msg        |   |SENDMAIL: Pending Msgs     |
|                           |   +---------------------------+   +---------------------------+   +---------------------------+   +---------------------------+
|        SENDMAIL           |   |                           |   |                           |   |                           |   |                           |
|                           |   | Username:                 |   |    app SENDMAIL           |   | Send-to:[johnny@apl.com]  |   | To|Status--|Send          |
|  [sign-up-with-mail-app]  |   | +-----------------------+ |   |    would like to send     |   | Message Body:             |   | +-|Approved|[pay $2]----+ |
|                           |   | +-----------------------+ |   |    mail on your behalf    |   | +-----------------------+ |   | +-|--------|------------+ |
+---------------------------+==>| Password:                 |==>|                           |==>| |Dear, Johnny Ive       | |==>| +-|--------|------------+ |
|  * we send a physical     |   | +-----------------------+ |   |  * [approve]              |   | |I <3 You!!!            | |   |                           |
|  letter on your behalf    |   | +-----------------------+ |   |  * [decline]              |   | |              -tom     | |   |                           |
|  * all happens without    |   |                           |   |                           |   | |                       | |   |                           |
|  someone exposing their   |   |      [sign-up] or [login] |   |                           |   | +-----------------------+ |   |                           |
|  address to a sender      |   |                           |   |                           |   |                    [send] |   |                           |
+---------------------------+   +---------------------------+   +---------------------------+   +---------------------------+   +---------------------------+
```

### App's request address on behalf of user

This is an example where the app the `Mail App` is requesting to access an address for an `App` `Send Mail` on behalf of the `User` `thomasreggi`.

* Figure 1. User receiving email message to provide access to app on behalf of user. (the user of the app will never see the address)
* Figure 2. Login to the app, if not already logged in.
* Figure 3. Approve and select address or deny request.

```
+---------------------------+   +---------------------------+   +---------------------------+
|gmail.com                  |   |Mail App: Login            |   |Mail App: Authorize        |
+---------------------------+   +---------------------------+   +---------------------------+
|    |                      |   |                           |   |    app SENDMAIL wants     |
|    | app SENDMAIL         |   | Username:                 |   |    to access your         |
|    | would like to        |   | +-----------------------+ |   |    address on behalf of   |
|    | access your address  |   | +-----------------------+ |   |    thomasreggi            |
|    | on behalf of         |==>| Password:                 |==>|                           |
|    | thomasreggi          |   | +-----------------------+ |   |  * [approve]              |
|    |                      |   | +-----------------------+ |   |  - [address dropdown]     |
|    | [login]              |   |                           |   |  - [auto-accept requests] |
|    |                      |   |      [sign-up] or [login] |   |  * [decline]              |
|    |                      |   |                           |   |                           |
+----+----------------------+   +---------------------------+   +---------------------------+
```

### Else not shown here

* Creating an app
* Validating a url

## API

Here's a bit of technical flow of how I'd see the API working. But first a primer on what we're collecting.

There are two main distinctions about the app.

1. This is an app that allows a user to store their address in one centralized place.
2. This is an app that allows a user to store addresses and keep track of what mail they're getting.

There's a lot of phrasing like `wants to access you address` above, or `would like to access your address`. But why? Why would anyone want your address? It's most-likley to send you something. The idea is that instead of just innocently accessing an address the wording should probably be changed to `wants to send you something`. Reguardless of the wording there's another psychological hump my developer brain needs to get over. Why would an app say `jcrew.com` request your address more than once? Once they have it they can do whatever they want with it. They can even sell it to another third party. The only reason I can rationally think they'd need to request it again is to see if the address has been changed. This is perfectly valuable. If we're collecting information every time they make a request to get the address about why the third-party needs. Then we have information about every pacakge, parcel, or letter a person is receiving. Most importantly we have real-time data.

The way I see it is the app would be a bit of both. In the case for user-to-user address request I'm not gonna log back in and update that I sent you something. I'm just gonna take the address and run. But in the case of an e-commerce app, it's not that much more work to send information programatically.

Here's what all this looks like in an api.

### Send mail: `get address` request

When a third party requests an address for instance on the checkout page of a store, I click the button and provide access. There's information that goes along with that request. The store needs to provide some information.

#### Buffer Time

Remember when I  mentioned `buffer time` the time that it takes for a store, vendor, supplier to ship something? This is where that comes in. If you go to flows for `App Login: E-Commerce Checkout` and see `figure 3` the `buffer time` would be displayed to the user there, it would say something like. You have 3 days to change your addres.

This all allows for the following. When an address is updated by the user then they will be shown a list of authorized sites and apps where the buffer time is still open, a webhook can be sent to these apps. For example if I buy something from `jcrew.com` and use this app, submit my address and feel like changing my address from my `home` to the `office` retro-activley after purchase, I can do so if there is still buffer time.

Again this would only work if the app has `buffer_time` and is subscribed to update address webhook.

#### Message

The `message` is a required field that is visible to the user for every request.

#### Example

Request:

```
{
  "message": "Ordered Teddy Bear",
  "buffer_time": 259200 // 3 days in seconds
}
```

Response:

```
{
  "id": "1234567890qwertyuio1234567890",
}
```


### Send mail: `mail sent` request

The `mail sent` request must be sent sometime following a `get address` request. An app or site will not be able to request an updated address untill they send this request.

#### Id

Id is a required field, the app or site must store the id and use it for this.

#### Tracking information

Tracking information can be inserted into this request and will be shown to the user.

#### Example

Request:

```
{
  "id": "1234567890qwertyuio1234567890",
  "tracking_numbers" : ["1Z2345"],
  "tracking_company" : "null",
  "tracking_urls" : ["http://www.google.com/search?q=1Z2345"]
}
```

Response:

```
{}
```

### Dashboard

#### Managing parcel / address requests

From this menu you can access each request ideally filter out some of this infromation so you can better manage it.

Features:

* filter out information from each request
* Search for specific user / site / app
* If buffer time is available be able to route the parcel to another address.

```
- Site "jcrew.com" requested your latest address.
    * This happened on September 12th, 2015 at 10:01am.
    * The request was auto-approved on September 12th, 2015 at 10:01am.
    * The address selected was "Office", which at the time was set to:
      * 1 Infinite Loop, Cupertino CA 95014
    * Future requests from "jcrew.com" will remain auto-approved.
    * Parcel is shipped, you have 2.3 hours left to change your address.

- User "thomasreggi" requested your address.
    * This happened on September 9th, 2015 at 9:41pm.
    * The request was approved on September 9th, 2015 at 3:31pm.
    * The address selected was "Office", which at the time was set to:
      * 1 Infinite Loop, Cupertino CA 95014
    * Future requests from "thomasreggi" will be auto-approved.
    * Parcel has been shipped, tracking code:
      * http://www.dhl.com/search?q=1Z2345

- User "iSelliPhone" requested your address.
    * This request was via ebay.com.
    * This happened on September 8th, 2015 at 4:41pm.
    * The request was approved on September 8th, 2015 at 4:41pm.
    * The address selected was "Home", which at the time was set to:
      * Rush Medical Group, 1221 North Washington Street, Livingston, AL 35470
    * Future requests from "iSelliPhone" will not be auto-approved.
    * Parcel has been shipped
```

#### Managing addresses

```
- "Office"
  * 1 Infinite Loop, Cupertino CA 95014
- "Home"
  * Rush Medical Group, 1221 North Washington Street, Livingston, AL 35470
- "Parents House"
  * California Hospital Medical Center, 1401 South Grand Avenue, Los Angeles, CA 90015
```

If I edit one, I would see the following.

```
(- Edit address form shown)
(If there is available app / site with buffer time this would show)
- This will send a reqeust to update your address across the following sites.
  - Site "jcrew.com" parcel "Red pants" with buffer time of 2 hours 3 minutes
  - Site "ebay.com" for user "iSelliPhone" parcel "iphone 19" with buffer time of 1 hour 29 minutes
  >>> [update]
```

If I want to delete one I'd have to map the old address to another existing one.

```
- To delete "Office" please select another address to map these parcels to
  * "Home"
  * "Parents House" < Select

(If there is available app / site with buffer time this would show as well)

- This will send a reqeust to update your address across the following sites.
  - Site "jcrew.com" parcel "Red pants" with buffer time of 2 hours 3 minutes
  - Site "ebay.com" for user "iSelliPhone" parcel "iphone 19" with buffer time of 1 hour 29 minutes
  >>> [update]
```

Now all requests for "Office" will go to "Parents House". Note that webhooks aren't sent to every authorized app / site on delete and update. Webhooks would only be sent to apps / sites that have webhooks enabled and in which there is a buffer time for a specific product.
