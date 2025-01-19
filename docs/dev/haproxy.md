# HAproxy
- [HAproxy docs](https://docs.haproxy.org/3.1/intro.html)

Come fare routing tra diversi servizi usando il nome DNS. Progetto di laboratorio, non replicare in produzione.

Setup:
- entry point (firewall): espone sull'interffaccia outside la VM con HA Proxy su porta 80
- il DNS locale risolve i nomi seguenti con l'ip dell'entry point (outside firewall):
    - factorio-1.lab
    - firefly.lab

- HA proxy fa smistamento del traffico in base al nome in entrata riducendo i NAT sul firewall per ogni applicazione a discapito della sicurezza.
    - factorio-1.lab > 10.0.2.3:80/health
    - firefly.lab > 10.0.5.1:3001

NB: necessario che l'entry point sia raggiunbile in porta 443 per maggiore sicurezza. L'utilizzo della porta 80 è una pigrizia da laboratorio. 


```bash
# Force the apt package management tool to assume “yes” to any questions that pop-up during the installation process.s
sudo apt install haproxy -y

cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
nano /etc/haproxy/haproxy.cfg

# Verifica integrità della configurazione
haproxy -c -f /etc/haproxy/haproxy.cfg

sudo systemctl restart haproxy
sudo systemctl status haproxy

sudo tail -f /var/log/haproxy.log
```

```config
global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin
        stats timeout 30s
        user haproxy
        group haproxy
        daemon

        # Default SSL material locations
        ca-base /etc/ssl/certs
        crt-base /etc/ssl/private

        # See: https://ssl-config.mozilla.org/#server=haproxy&server-version=2.0.3&config=intermediate
        ssl-default-bind-ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
        ssl-default-bind-ciphersuites TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256
        ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
        errorfile 400 /etc/haproxy/errors/400.http
        errorfile 403 /etc/haproxy/errors/403.http
        errorfile 408 /etc/haproxy/errors/408.http
        errorfile 500 /etc/haproxy/errors/500.http
        errorfile 502 /etc/haproxy/errors/502.http
        errorfile 503 /etc/haproxy/errors/503.http
        errorfile 504 /etc/haproxy/errors/504.http

frontend http_front
    bind *:80
    acl host_factorio hdr(host) -i factorio-1.lab
    acl host_firefly hdr(host) -i firefly.lab

    use_backend factorio_backend if host_factorio
    use_backend firefly_backend if host_firefly
    default_backend default_backend

backend factorio_backend
    # L'API custom del server è raggiungibile solo su /health
    http-request set-path /health
    server factorio_server 10.0.2.3:80 check

backend firefly_backend
    server firefly_server 10.0.5.1:3001 check

backend default_backend
    errorfile 503 /etc/haproxy/errors/503.http
```