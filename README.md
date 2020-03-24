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
traefik.docker.network | Docker network to use| bridge | No
traefik.loglevel | Log level | INFO | No
traefik.acme.email | Email used for Let's Encrypt cert req. | none | Yes

Example config
--------------

```
traefik:
  letsencrypt: true
  docker:
    network: "foobar"
  acme:
    email: "foobar@foobar.com"
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