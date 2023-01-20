---
title: "Axum"
date: 2023-01-09T11:18:38-08:00
draft: false
tags:
  - dev
---

Alright FINE I'll write about software development. It only took me 2 freakin posts to run out of anything else to write about.

## Rust

I write a fair amount of Rust at work and try to keep up with hobby projects at home too. Recently I've been writing a website to download Twitter data for people who, like me, expect Twitter to crash and burn any day now but want to save all their funny Likes somewhere permanent. Like this one:

{{< rawhtml >}}
<blockquote class="twitter-tweet" data-dnt="true" data-align="center"><p lang="en" dir="ltr">Hey Boy. Ya Jeans Look Shitted In</p>&mdash; wint (@dril) <a href="https://twitter.com/dril/status/1602613307134861312?ref_src=twsrc%5Etfw">December 13, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
{{< /rawhtml >}}

There were a few tools to do this out there already, but I either couldn't get them to work, they cost money, or they didn't save the data in a very helpful format. Anyway this project may never see the light of day, but I will at least put down some things I learned so far about the Axum web framework.

## Rust Web

At work, we use Rust for our core delivery stack, our Kafka consumer applications, and recently for a couple gRPC microservices. However, we don't yet have any straight-up HTTP applications written in Rust, so I'm not really familiar with any HTTP frameworks. Based on Google results, it seems like Rocket is the most popular, but for some reason I decided to use [Axum](https://docs.rs/axum/latest/axum/) for this (I honestly can't remember why I picked it, maybe there was a sexy blog post about it on HN that day, idk). Like most of my hobby projects, I probably could have done this a lot faster with Ruby/Rails, but the idea of starting a Rails project these days is a huge bummer for some reason.

## Axum

Axum has the usual HTTP frameworky things, like handlers and middleware. Handlers are basically functions that accept any number of "extractors", which implement `FromRequest`, and return anything that implements `IntoResponse`.

### `IntoResponse`

A lot of things implement this trait; I think it's meant to be implemented on anything you would reasonably expect to represent an HTTP response. One of the most basic ones is the tuple `(http::StatusCode, &'static str)`, i.e. the status and the text body. There are more sophisticated implementations too, like [`Sse`](https://docs.rs/axum/latest/axum/response/struct.Sse.html), which is a server-sent event struct that wraps a `Stream` and writes each stream result to the client via websockets.

### `FromRequest` and Extractors

Like `IntoResponse`, there are a lot of things that implement this trait. It seems like the general term for them are "extractors", because they extract information encoded in the request. For instance here's how you get the JSON request body:

```rust
async fn json_handler(Json(my_struct): Json<MyStruct>) {
  // ...
}
```

`Json<T>` is an extractor that deserializes the JSON request body into any struct `T`, which must implement `serde::Deserialize`. In the function body, `my_struct` is the resulting instance of `MyStruct`.

### Custom Extractors

Writing extractors is easy! You just need to implement either `FromRequest` or `FromRequestParts`. The latter is a little simpler I think. It has an associated type `Rejection`, which is sort of like the error type that is returned when the extractor fails, and which must implement our old friend `IntoResponse`. For example in my Twitter project I wrote a custom extractor for fetching OAuth user data from the session. _Buckle up_:

```rust
#[async_trait]
impl<S> FromRequestParts<S> for TwitterUser
where
    S: Send + Sync,
{
    type Rejection = AuthRedirect;

    async fn from_request_parts(parts: &mut Parts, _: &S) -> Result<Self, Self::Rejection> {
        let session = parts.extract::<ReadableSession>().await.map_err(|e| {
            error!(error = %e, "extracting user session");
            AuthRedirect
        })?;

        let user = session
            .get::<TwitterUser>(TWITTER_USER_KEY)
            .ok_or(AuthRedirect)?;

        Ok(user)
    }
}
```

So `from_request_parts` is the required method here. `TwitterUser` is a struct containing things like the user's ID and access token. These are stored in the session after the whole OAuth dance (which was a bit of a headache to get working, but that's for another post). `AuthRedirect` is a unit struct that implements `IntoResponse` such that it redirects to the login page if the user doesn't exist in the session or is malformed.

The interesting bit here is that you can call other extractors from within your extractor. Notice the `parts.extract::<ReadableSession>()` part; that's just another extractor that comes from the `axum_sessions` crate.

And finally, using the extractor in a handler is beautifully declarative:

```rust
async fn handler(user: TwitterUser) -> impl IntoResponse {
  // ...
}
```

The handler just says "Give me a `TwitterUser`, I don't care where it comes from". And axum is like "You got it". And you know that once you're inside the handler function, you have valid Twitter data to work with.

Bingo Bango Bongo
