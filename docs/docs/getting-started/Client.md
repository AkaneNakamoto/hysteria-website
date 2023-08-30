# Client

This tutorial will guide you through the setup of a Hysteria client. Similar to the server, Hysteria is very flexible and the options covered in this tutorial are only a subset of what's available. We will focus on the HTTP and SOCKS5 proxy modes. **For further customization, please refer to [Full Client Config](../advanced/Full-Client-Config.md).**

The steps were performed in a Linux environment, but should be similar on other platforms.

## Prerequisites

- A Hysteria server you can connect to

## Creating configuration file

Assuming you have already downloaded the executable for your platform into a directory, say `hysteria-linux-amd64-avx`. Create a `config.yaml` file in the same directory.

**Be sure to replace the values according to your server's settings and your specific needs.**

```yaml
server: your.domain.net:443 # (1)!

auth: Se7RAuFZ8Lzg # (2)!

bandwidth: # (3)!
  up: 20 mbps
  down: 100 mbps

socks5:
  listen: 127.0.0.1:1080 # (4)!

http:
  listen: 127.0.0.1:8080 # (5)!
```

1. Replace with your server's address
2. Replace with the password you set on the server
3. See [below](#bandwidth) for more information on bandwidth
4. Replace with the address you want the SOCKS5 proxy to listen on
5. Replace with the address you want the HTTP proxy to listen on

### Bandwidth

**Set the `up` and `down` bandwidth values to match the maximum rates of your current Internet connection.** Although highly recommended, these settings are optional. If you're unsure about the bandwidth on one or both sides, you can either set only one side or remove this bandwidth section entirely. The behavior controlled by these bandwidth settings is described in the [Bandwidth section of Full Client Config](../advanced/Full-Client-Config.md#bandwidth).

### TLS

If your server uses a self-signed certificate, you can either specify the CA to trust in the configuration file, or use the `insecure` option to disable verification altogether. If you choose the `insecure` setting, we strongly recommend using the `pinSHA256` option to verify the server's certificate fingerprint.

=== "CA"

    ```yaml
    tls:
      ca: ca.crt # (1)!
    ```

    1. Replace with the path to the CA certificate file

=== "Insecure"

    ```yaml
    tls:
      insecure: true
    ```

    > **WARNING:** Using `insecure` alone is NOT recommended, as it makes your connection susceptible to MITM attacks. See the next tab for a better alternative.

=== "Insecure + pinSHA256"

    ```yaml
    tls:
      insecure: true
      pinSHA256: BA:88:45:17:A1... # (1)!
    ```

    1. You can obtain the fingerprint of your certificate using openssl: `openssl x509 -noout -fingerprint -sha256 -in your_cert.crt`

## Running the client

Start the client by the following command:

=== "Default filename (config.yaml)"

    ```bash
    ./hysteria-linux-amd64-avx
    ```

=== "Custom filename"

    ```bash
    ./hysteria-linux-amd64-avx -c whatever.yaml
    ```

> **TIP:** You can also use `./hysteria-linux-amd64-avx client`, but client is the default mode so it can be omitted.

If you see the log message "connected to server" and no errors, congratulations 🎉! You have successfully deployed a Hysteria client.

You will also see a log message "use this URI to share your server" with a URI. This URI can be used as the `server` value in the client's configuration file. Since it already contains the password and a few other settings, you won't need to specify them separately. For more information on the URI format, please refer to [URI Scheme](../developers/URI-Scheme.md).

This tutorial won't cover the details of how to use an HTTP or SOCKS5 proxy, as there are plenty of resources available online. For those completely new to proxies, we recommend the [Proxy SwitchyOmega browser extension](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif) for Chrome and Firefox as a good starting point.
