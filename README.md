[![create and publish docker image](https://github.com/haq/caddy-cf/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/haq/caddy-cf/actions/workflows/docker-publish.yml)

# caddy-cf

A caddy docker image containing the [cloudflare](https://github.com/caddy-dns/cloudflare) & [dynamicdns](https://github.com/mholt/caddy-dynamicdns) modules.

## docker-cli

### volumes

```shell
docker volume create caddy_config
docker volume create caddy_data
```

### container

```shell
docker run -d \
  --name caddy \
  --restart unless-stopped \
  --network host \
  -e CLOUDFLARE_API_TOKEN=cf_api_token \
  -v "$PWD"/Caddyfile:/etc/caddy/Caddyfile \
  -v caddy_config:/config \
  -v caddy_data:/data \
  ghcr.io/haq/caddy-cf
```

## caddyfile

```Caddyfile
{
    email email_address_for_notifications
    dynamic_dns {
        provider cloudflare {env.CLOUDFLARE_API_TOKEN}
        domains {
            domain.com sub
            domain.com sub1
        }
        check_interval 5m
        ip_source simple_http https://icanhazip.com
        versions ipv4 ipv6
        ttl 1h
    }
}

(cloudflare) {
    tls {
        dns cloudflare {env.CLOUDFLARE_API_TOKEN}
    }
}

sub.domain.com {
    import cloudflare
    reverse_proxy ip:port
}

sub1.domain.com {
    import cloudflare
    reverse_proxy ip:port
}
```
