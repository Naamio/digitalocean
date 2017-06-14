# DigitalOcean

A crate for interacting with the Digital Ocean API.

While browsing this documentation please feel encouraged to reference the
[DigitalOcean docs](https://developers.digitalocean.com/documentation/v2/).

## A Basic Example

```rust,no_run
use digitalocean::prelude::*;
use std::env;

let api_key = env::var("API_KEY")
    .expect("API_KEY not set.");
let client = DigitalOcean::new(api_key)
    .unwrap();

Droplet::list()
    .execute(&client);
```

## Usage Fundamentals

All values ([`Domain`](api/struct.Domain.html), [`SshKey`](api/struct.SshKey.html), etc) can 
be found in the [`api`](api/index.html) module.

Calling an action will return a [`Request<_,_>`](request/struct.Request.html) type. For example 
[`Droplet::create()`](api/struct.Droplet.html#method.create) will create a
[`Request<Create, Droplet>`](request/struct.Request.html#method.ssh_keys). This type may then
have specific futher functions to futher build up the request or transform it into some other
request.

```rust,no_run
use digitalocean::DigitalOcean;
use digitalocean::api::Domain;

// Gets details of a specific domain.
let req = Domain::get("foo.com");

// Get the records for that domain instead (futher build the request)
let req = req.records();
// Get the records of a domain without having a prior request.
let req = Domain::get("foo.com").records();

// Create a new record for a domain
let req = Domain::get("foo.com").records().create("CNAME", "test", "127.0.0.1");
```

In order to realize any action [`.execute()`](request/trait.Executable.html#tymethod.execute)
must be called with a [DigitalOcean](struct.DigitalOcean.html#method.new) client. It is also 
possible to call [`do_client.execute(some_request)`](struct.DigitalOcean.html#method.execute).

In order to use the entire API it is recommended to reference the various `Request` types.

## Design

The crate is founded on a few design considerations:

* Keep things simple and generic.
* Map closely to the DigitalOcean API.
* `Request`s are agnostic over `Client`s.
* It should be impossible to make an invalid API request.
* Use static dispatch as much as possible.
* Only the bare minimum amount of information should be carried around.
* Allow for easy construction of separate clients (`hyper`, etc.)
* No caching (yet). (DigitalOcean does not have [ETags](https://en.wikipedia.org/wiki/HTTP_ETag))

## Debugging

This crate uses the [`log`](https://doc.rust-lang.org/log/log/index.html) crate. You can see `digitalocean` logs by passing an environment variable such as:

```bash
RUST_LOG=digitalocean=debug cargo run
```

## Development Status

This crate is in a prototype state.

Not all endpoints have been fully end-to-end tested on the production DigitalOcean API. It's very
likely that some endpoints will have parsing errors due to unexpected values returned from the API.

**If something does not work please file a bug!**

Feedback, patches, and new features are encouraged. 
Please just open an issue or PR!