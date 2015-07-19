# Centralized Place for Your Addresses and Mail

> tl;dr Idea for a web app that allows you to login and save your address. Then have the ability to connect your addresses across multiple sites in a centralized fashion. Think facebook connect for addresses. Every site that needs your address (think e-commerce) would have a "connect" button that would assign an address from a possible list. Third-party apps would request your latest address whenever they want to send you something.

The web is a vibrant and thriving place. While there may be a ton of advancement happening every day to make our lives easier, some of the things we do everyday are still traditional and repetitive. One of those things is managing and distributing our physical addresses. How can we make managing addresses easier across the entire web? This document is a specification for a service to rectify this very issue.

## Current Problems

1. Every site stores your address on it's own servers. That means it's decentralized. Let's say you move where you live, you can't just update your address in one place and have this change for every site. If you signup for a new service you have to type the whole thing in again.

2. When you place an order online, if you enter the wrong address (which would happen less frequently with this service) you have an allotted amount of time before that order ships, I call this time the `buffer time`. This service aims to help manage this with a high level of efficiency and detail. Currently the only way to change an address on most sites is to get in touch with a person directly, and have them check if the orders been processed by a fulfillment warehouse and manually update an address if not. Some sites do urge that you verification your address in an `order confirmation` email. Some provide a way to change your address programmatically after order creation. But still the `buffer time` isn't standardized, and it's not the same for every site.

3. Privacy. When using the service you'll be able to list who has had access to an address, when, what address, and how frequently. You can even block other users, apps, or sites, from requesting your latest address.

4. Have you ever wanted to send someone physical mail and you didn't know their address? This solves that, for everyone, and eventually it might not be weird. Once the recipient has their address in the system, they never have to write it again. Only approve access to other users.

## Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Definitions](#definitions)
  - [`App`](#app)
  - [`Request`](#request)
  - [`User`](#user)
  - [`App-Api`](#app-api)
  - [`Third-Party-App`](#third-party-app)
  - [`Site`](#site)
- [Mockups](#mockups)
  - [`App` Homepage & Dashboard](#app-homepage-&-dashboard)
  - [`User` to `User` Request](#user-to-user-request)
  - [`User` to `User` Authorize](#user-to-user-authorize)
  - [`Site` to `User` Request-Authorize](#site-to-user-request-authorize)
  - [`Site` for `User` to `User` Request-Authorize](#site-for-user-to-user-request-authorize)
  - [`Third-Party-App` on behalf of `User` for `User`](#third-party-app-on-behalf-of-user-for-user)
- [API](#api)
  - [What is a `webhook`?](#what-is-a-webhook)
  - [`/get-address.json`](#get-addressjson)
    - [`buffer_time`](#buffer_time)
    - [`message`](#message)
    - [Example](#example)
  - [`/mail_sent.json`](#mail_sentjson)
    - [`id`](#id)
    - [`tracking`](#tracking)
    - [Example](#example-1)
- [Dashboard](#dashboard)
  - [Managing parcel / address requests](#managing-parcel--address-requests)
  - [Managing addresses](#managing-addresses)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Definitions

### `App`

Whenever I refer to `App` I mean everything written in this document including the finished software and the idea. Usually referred to as "the `App`".

### `Request`

The term `Request` is used a lot in the following sections. It generally means to send something digitally. It may mean to ask for something digitally as well.

### `User`

A `User` is a person, just like you and me. They have the ability to create a `Third-Party-App` or a `Site`.

1. They can ask a `User` for a `Address`.
  * Can `User` `Thomas Reggi` access your address to send you mail?

### `App-Api`

The `App-Api` publicly available (through [oAuth](http://oauth.net/) and cryptography) set of urls on the server that allow a `Third-Party-App` to connect to the `App`. When connected the `Third-Party-App` has access to `Actions` and `Data`. For instance making a request to the endpoint `http://App.com/api/address/thomasreggi.json` would get the `Addresses` for the `User` `thomasreggi` in the `.json` format.

### `Third-Party-App`

A `Third-Party-App` is a software created as a service that utilizes the `App-Api` to interact with the `App`. They can contain their own `Addresses`.

The numbered line is the privilege and the line below is what the `User` would approve or decline.

1. They can ask a `User` for a `Address`.
  * Can `Third-Party-App` `Example App` access your address to send you mail?
2. They can ask a `User` for a `Address` for a `User`.
  * Can `Third-Party-App` `Example App` give your address to `User` `Thomas Reggi` so they can to send you mail?
3. They can ask a `User` for a `Address` on behalf of a `User`.
  * Can `Third-Party-App` `Example App` send you mail on behalf of `User` `Thomas Reggi`?

### `Site`

A `Site` is a `Third-Party-App` where the url has been verified. For example if an employee at `jcrew.com` wanted to integrate the `App` into their store, they would want to be represented as `jcrew.com` so they would follow the steps for [TXT Record Domain Verification](https://en.wikipedia.org/wiki/TXT_Record). Without this feature if someone squatted on the `Third-Party-App` name `jcrew` then the real `jcrew` would be out of luck. Plus the verification means we could show the visually show that the `Site` is a verfied which midigates [Phishing](https://en.wikipedia.org/wiki/Phishing).

The options are the same, the message is a little different.

1. They can ask a `User` for a `Address`.
  * Can `Site` `amazon.com` access your address to send you mail?
2. They can ask a `User` for a `Address` for a `User`.
  * Can `Site` `amazon.com` give your address to `User` `Thomas Reggi` so they can to send you mail?
3. They can ask a `User` for a `Address` on behalf of a `User`.
  * Can `Site` `amazon.com` send you mail on behalf of `User` `Thomas Reggi`?

Amazon is the perfect example for this because they allow all three. Not all sites need all three, but that's ok.

Here's where Amazon correlates.

1. Buy something from Amazon. (ex. Kindle)
2. Buy something from a Amazon seller, where they manage their inventory. (ex. Used Book)
3. Buy something from a Amazon seller, where amazon fulfillment manages their inventory.

## Mockups

Below are mockups that describe different webpages and the flow from one page to the next. Each square is a page, they move from left to right, you might need to scroll to the right to see some of them.

### `App` Homepage & Dashboard

1. View Homeapge
2. Login or signup. If already logged in redirect's to next page.
3. View dashboard

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

### `User` to `User` Request

1. Request addresses from user email (will be asked to signup / login)
2. Shows list of Pending Requests.

These Events lead to [`User`-to-`User` Authorize](#user-to-user-authorize)

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

### `User` to `User` Authorize

These events are the effect from [`User`-to-`User` Authorize](#user-to-user-authorize)

1. User receiving email message to provide access to their address
2. Login or signup. If already logged in redirect's to next page.
3. Approve and select address or deny request.

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


### `Site` to `User` Request-Authorize

1. `User` is checking out on a website and needs to connect address, signs in.
2. Login to the `App`, if not already logged in.
3. Approve `Site` and select address or deny request.

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

### `Site` for `User` to `User` Request-Authorize

1. `User` is checking out on a website and needs to connect address, signs in.
2. Login to the `App`, if not already logged in.
3. Approve and select address or deny request.

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

### `Third-Party-App` on behalf of `User` for `User`

This is an idea for an `Third-Party-App` called `Send Mail`.

1. The landing page to the `Third-Party-App` with login button.
2. Login to the `App`, if not already logged in.
3. Approve or decline the `Third-Party-App` that will send mail on your behalf.
4. Craft a message, enter the email address of the recipient, or the `User`.
  * These Events lead to [`User`-to-`User` Authorize](#user-to-user-authorize)
5. View pending messages.
  * If the `User` doesn't have an account they will be prompted to make an account.
  * If the `User` has auto-approved messages from you as a user, or auto-accepts mail from this app it will be auto accepted.
  * If the status of a message is approved then the senders address is in the system and mail can be sent. Pay to send it.

> tl;dr The `Third-Party-App` allows you to proxy mail and sends it on the behalf of another `User`. The `Third-Party-App` is responsible for sending the mail. Which means that the `SenderUser` never sees the `RecipientUser's` address (just make sure the `Third-Party-App's` return address is on the envelope').

This `Third-Party-App` would allow a `User` to send another `User` mail without the sender ever getting hold of the recipients address. I thought of this app in tandem with a physical business. The idea being that the creator of this `Third-Party-App` would be the proxy mail between the two users. This `Third-Party-App` would do the mailing on behalf of a customer. All that's really needed to pull this off is a list of all letters to mail for the day, a box of [envelopes with two plastic windows](http://i.imgur.com/6wFDumi.jpg) and a ream of 8.5"x11" paper. Using the `App-Api` the creator can export a all the PDF letters for a day, fold the, stamp the envelopes, and mail them. In this case someone would need to pay the `Third-Party-App` creator to mail the letter. If under some promotion this may be able to be to funded by the Fan's recipient in bulk. Say Neil Degrasse Tyson or Bill Nye was running a promotion (ideally taking questions about scientific inquiries) then all mail to him would be paid for by a third party and not the sender. There are also less involved ways of sending physical mail, there are many api's that already exist that allow you to do it. This `Third-Party-App` could also be harnessed to send people in your contacts (that you have the email of).

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

## API

Here's a bit of technical flow of how I'd see the API working. But first a primer on what we're collecting.

There are two main distinctions about the `App`.

1. This is a service that allows a user to store their address in one centralized place.
2. This is a service that allows a user to store addresses and keep track of what mail they're getting.

There's a lot of phrasing like `wants to access you address` above, or `would like to access your address`. But why? Why would anyone want your address? It's most-likley to send you something. The idea is that instead of just innocently accessing an address the wording should probably be changed to `wants to send you something`. Regardless of the wording there's another psychological hump my developer brain needs to get over. Why would an site like `jcrew.com` request your address more than once? Once they have it they can do whatever they want with it. They can even sell it to another third party. The only reason I can rationally think they'd need to request it again is to see if the address has been changed. This is perfectly valuable. If we're collecting information every time they make a request to get the address about why the third-party needs. Then we have information about every package, parcel, or letter a person is receiving. Most importantly we have real-time data.

The way I see it is the service would be a bit of both. In the case for user-to-user address request I'm not gonna log back in and update that I sent you something. I'm just gonna take the address and run. But in the case of an e-commerce store, it's not that much more work to send information programmatically.

### What is a `webhook`?

Before we dive in I just wanted to define `webhook`. A `webhook` allows for a `Third-Party-App` to subscribe to a specific event. What does that mean?

A `Third-Party-App` is software that runs on a server. It could have urls like `/api/subscribe-webhook/address.json` and whenever someone (or another server) visits that page the `Third-Party-App` knows, and can run specific software. So essentially a `webhook` is a way for two servers to talk to each other securely.

Let me give an analogy.

Let's say your a `comic book aficionado` and you always like to get the most recent comic book from the `comic book shop` as soon as it arrives. You pickup the telephone and tell the clerk give me a call whenever an issue of a specific comic comes in (lets say `superman`). The clerk says, alright, will do, and hangs up the phone. You wait patiently with your phone on and close by for a call. Ring! Ring! You pickup the phone, and it's the clerk and he tells you there's a new comic in, issue #23 just arrived.

In this analogy the `comic book aficionado` is the `Third-Party-App`, the `comic book shop` is the `App`. The act of making the call and telling the clerk what comic you'd like is called `subscribing to a webhook`. The `webhook` is the `superman` comic. The clerk saying alright is the subscribing process being a success. You waiting patiently is the `Third-Party-App` having an open url and being able to accept requests to it's server. You picking up the phone is the `Third-Party-App` responding to the `webhook` request. Lastly the clerk telling you what issue is the `webhook payload`.

What's the alternative to this anology? If `the comic book shop` clerk wasn't so nice to give you a call, and assuming they still had a telephone. You would have to call everyday, perhaps even multiple times a day. That's pretty inefficient and time-consuming. When it's code you can make these kinds of requests a thousand times a minute, this means a lot of requests come back with no new data. This is what most servers and still do back when RSS was all the rage and it's really inefficient.

### `/get-address.json`

When a third party requests an address for instance on the checkout page of a store, I click the button and provide access. There's information that goes along with that request. The store needs to provide some information.

#### `buffer_time`

Remember when I  mentioned `buffer time` the time that it takes for a store, vendor, supplier to ship something? This is where that comes in. If you go to mockup for [`Site` to `User` Request-Authorize](#site-to-user-request-authorize) the `buffer time` would be displayed to the user in page 3, it would say something like. You have 3 days to change your address.

This all allows for the following. When an address is updated by the user then they will be shown a list of authorized `Third-Party-Apps` (and `Sites`) where the `buffer time` is still open, a `webhook` can be sent to these `Third-Party-Apps` (and `Sites`). For example if I buy something from `jcrew.com` and use the `App`, submit my address and feel like changing my address from my `home` to the `office` retro-activley after purchase, I can do so if there is still buffer time.

Again this would only work if the `Third-Party-App` has `buffer_time` enabled and is subscribed to update address `webhook`.

#### `message`

The `message` is a required (perhaps not for `User`-`User`) field that is visible to the user for every request.

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


### `/mail_sent.json`

The `mail sent` request must be sent sometime following a `get address` request. An`Third-Party-App` (or `Site`) will not be able to request an updated address until they send this request.

#### `id`

`Id` is a required field, the `App` or site must store the id and use it for this.

#### `tracking`

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

## Dashboard

### Managing parcel / address requests

From this menu you can access each request ideally filter out some of this information so you can better manage it.

Features:

* filter out information from each request
* Search for specific `User` / `Site` / `App`
* If `buffer time` is available be able to route the parcel to another address.

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

### Managing addresses

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
(If there is available `Third-Party-App` (or `Site`) with `buffer time` this would show)
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

(If there is available `Third-Party-App` (or `Site`) with `buffer time` this would show)

- This will send a reqeust to update your address across the following sites.
  - Site "jcrew.com" parcel "Red pants" with buffer time of 2 hours 3 minutes
  - Site "ebay.com" for user "iSelliPhone" parcel "iphone 19" with buffer time of 1 hour 29 minutes
  >>> [update]
```

Now all requests for "Office" will go to "Parents House". Note that `webhooks` aren't sent to every authorized `Third-Party-App` (and `Site`) on delete and update. Webhooks would only be sent to `Third-Party-Apps` (and `Sites`) that have `webhooks` enabled and in which there is a `buffer time` for a specific product.
