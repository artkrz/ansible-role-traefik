Traefik
=========

Traefik Ansible role with Let's Encrypt support.

Requirements
------------


Role Variables
--------------

 Variable | Description | Default value | Required
------------ | ------------- | ------------- | -------------
traefik.config_path | Path where config is stored (persistant volume) | /opt/docker_volumes/traefik | No
traefik.docker_container.networks | Docker networks list to use | bridge | No
traefik.config | YAML config that will be render as traefik.yaml | No | Yes

Example config
--------------

The `traefik.config` will accept any config structure as long as it's valid, basically it renders it as YAML.

```
traefik:
  docker_container:
    networks:
      - name: bridge
      - name: foobar
  config:
    log:
      level: "INFO"
    entryPoints:
      http:
        address: ":80"
      https:
        address: ":443"
    api:
      dashboard: true
    providers:
      docker:
        endpoint: "unix:///var/run/docker.sock"
        watch: true
        exposedByDefault: false
      file:
        filename: "/etc/traefik/traefik.yaml"
    certificatesResolvers:
      letsencrypt:
        acme:
          email: "foo@foobar.com"
    tls:
      options:
        tls12:
          minVersion: "VersionTLS12"
          cipherSuites: 
            - "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384"
            - "TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305"
            - "TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305"
            - "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256"
            - "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    http:
      middlewares:
        traefik-router-redirectscheme:
          redirectScheme:
            scheme: "https"
        traefik-router-auth:
          basicAuth:
            users: 
              - "admin:$abkaiusdhu43&S5ts*vFk0"
      routers:
        traefik-router-redirect:
          entrypoints: "http"
          rule: "Host(`traefik-dashboard.foobar.com`)"
          service: noop@internal
          middlewares: 
            - traefik-router-redirectscheme
        traefik-router:
          entrypoints: "https"
          rule: "Host(`traefik-dashboard.foobar.com`)"
          tls:
            certresolver: "letsencrypt"
          service: api@internal
          middlewares:
            - traefik-router-auth
```

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - traefik

License
-------

BSD

Author Information
------------------

If You like this role and would like to buy me :coffee: or :beer: You can do it via [PayPal](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=2NJYFLHNYJGU2&item_name=Ansible+Role+Development&currency_code=PLN&source=url)
