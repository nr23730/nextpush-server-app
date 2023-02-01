# NextPush - Server App

UnifiedPush provider for Nextcloud - server application 

## Requirement

[Your Nextcloud instance needs to have a Redis server installed and listening for connections.](https://docs.nextcloud.com/server/latest/admin_manual/configuration_server/caching_configuration.html)

## Installation

1. Install this Nextcloud application from the store: https://apps.nextcloud.com/apps/uppush .

2. Configure the reverse-proxy. ([See examples of reverse-proxy conf](reverse_proxy_examples))

    a. The reverse-proxy need to be configured for long timeout :

    _Nginx_: - Server Block
    ```
    proxy_connect_timeout   10m;
    proxy_send_timeout      10m;
    proxy_read_timeout      10m;
    ```

    _Apache_: - VirtalHost Block
    ```
    ProxyTimeout 600
    ```

    b. The reverse-proxy need to be configured without buffering :

    _Nginx_: - Server Block
    ```
    proxy_buffering off;
    ```

    _Apache_ (php configuration): - VirtalHost Block
    ```
    <Proxy "fcgi://localhost/" disablereuse=on flushpackets=on max=10>
    </Proxy>
    ```

## Gateways

### Matrix
The app can be used as a personal matrix gateway. It requires to pass requests to the path `/_matrix/push/v1/notify` to `/index.php/apps/uppush/gateway/matrix`.

[See examples of reverse-proxy conf](reverse_proxy_examples)

_Nginx_: - Server Block
```
location /_matrix/push/v1/notify {
    proxy_pass http://127.0.0.1:5000/index.php/apps/uppush/gateway/matrix;
}
```

_Apache_: - VirtalHost Block
```
ProxyPass "/_matrix/push/v1/notify" http://127.0.0.1:5000/index.php/apps/uppush/gateway/matrix
```

### Universal
The app can act as a universal gateway for applications with limited support for UnifiedPush. This is the case where an url can be registered on the app server but may be extended with things like `/notifications`.
Here your UnifiedPush endpoint can be posted to a gateway at `{somehost}/gateway/universal/{baseEncoded}.*` The gateway will then ignore the appended part and extract your token from the base64 encoded part.

[See examples of reverse-proxy conf](reverse_proxy_examples)

_Nginx_: - Server Block
```
location /gateway/universal {
    proxy_pass http://127.0.0.1:5000/index.php/apps/uppush/gateway/universal;
}
```

_Apache_: - VirtalHost Block
```
ProxyPass "/gateway/universal" http://127.0.0.1:5000/index.php/apps/uppush/gateway/universal
```

## Development

Clone the repository to __nextcloud/apps/uppush__ instead of installing from Nextcloud store.

```
git clone https://github.com/UP-NextPush/server-app/ nextcloud/apps/uppush
```

## Credit

This application has been inspired by [Nextcloud Push Notifier](https://gitlab.com/Nextcloud-Push/direct-push-proxy-v2)
