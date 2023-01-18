# proxypac

`proxypac` is a simple script for macOS that will serve a `proxy.pac` file on `localhost:8082`, and configure the system settings to use it. After the server stops, the system proxy will be disabled.

This can be a reasonable alternative on Safari to a tool like FoxyProxy for Firefox, if you are comfortable with writing PAC files. See [examples](#examples).

Safari is very lenient when using the PAC file, and may fall back to direct access if your proxy is down. That means this can easily leak DNS or actual data, so *don't use it for anything important*. Similarly, there may be a short delay after the PAC file is enabled (or disabled) before Safari notices.

## Install

- `git clone` this installation somewhere
- Place your proxy.pac file in the `root/` directory

## Configuration

Other than your `proxy.pac`, that's it. Port and root dir are hardcoded in the script.

## Usage

Run `proxypac` from any directory.

`proxypac start` will serve `proxy.pac` and enable proxy auto-configuration from that URL in system settings. Press Ctrl-C to terminate the server and disable the system proxy.

`proxypac stop` will also stop the server and disable the system proxy, in case you can't Ctrl-C your running server.

`proxypac status` will show any listening server and the system proxy auto-config status.

## Examples

See the Mozilla docs on [how to write PAC files](https://developer.mozilla.org/en-US/docs/Web/HTTP/Proxy_servers_and_tunneling/Proxy_Auto-Configuration_PAC_file).

Here's an example which will use a local SOCKS proxy just for a particular domain:

```js
function FindProxyForURL(url, host) {
    if (shExpMatch(host, "*.example.com")) {
        return "SOCKS localhost:1080;SOCKS5 localhost:1080";
    }
    return "DIRECT";
}
```

## SSL

In macOS 12 Monterey, using HTTP to access PAC files is deprecated in favor of HTTPS.
`http://` will still work, but a TLS handshake is attempted first on every connection,
despite the insecure protocol. This will result in lots of garbage in the logs.

This seems like suboptimal behavior, because of the explicit protocol and because port 8082
is earmarked as being for cleartext PAC transmission.

## Alternatives

Placing a proxy.pac file a public gist as per [this stackexchange post](https://apple.stackexchange.com/a/396773/287722) works, as long as your file is not secret, and you are OK with leaving it enabled all the time.
This will also take care of the SSL requirement introduced in macOS 12.
