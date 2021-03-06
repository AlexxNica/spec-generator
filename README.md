
# Spec Generator

This exposes a service to automatically generate specs from various source formats.

## API

Spec Generator has a single endpoint, which is a `GET /`. This endpoint accepts parameters on its
query string. If the call is successful the generated content of the specification is returned.

* `type` (required). The type of generator for this content. Currently the only supported value is
  `respec`.
* `url` (required). The URL of the draft to fetch and generate from.
* `publishDate`. The date at which the publication of this draft is supposed to occur.

### Errors

If a required parameter is missing or has a value that is not understood, the generator returns a
`500` error with a JSON payload the `error` field of which is the human-readable error message.

If the specific generator encounters a problem a similar error (mostly likely `500`) with the same
sort of JSON message is returned. Specific generator types can extend this behaviour. The `respec`
generator only returns `500` errors.

## Writing generators

Generators are simple to write and new ones can easily be added. Simply add a new one under
`generators` and load it into the `genMap` near the top of `server.js`.

Generators must export a `generate()` method which takes a URL, a set of parameters (from the list
of optional ones that the API supports), and a callback to invoke upon completion.

If there is an error, the callback's first argument must be an object with a `status` field being
an HTTP error code and a `message` field containing the error message. If the generator is
successful the first argument is `null` and the second is the generated content.

## Running

The generator for ReSpec requires a running X server; in a headless environment, you will need to run it in connection with a Xvfb instance:
```sh
Xvfb :3  &  # (where 3 is the display port)
DISPLAY=:3 node server.js
```

A sufficient (but perhaps more than necessary) set of packages in a Debian/Ubuntu environment is `xvfb x11-xkb-utils xfonts-100dpi xfonts-75dpi xfonts-scalable xfonts-cyrillic x11-apps clang libdbus-1-dev libgtk2.0-dev libnotify-dev libgnome-keyring-dev libgconf2-dev libasound2-dev libcap-dev libcups2-dev libxtst-dev libxss1 libnss3-dev gcc-multilib g++-multilib`.
