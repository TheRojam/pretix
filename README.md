# Pretix

Sets up and configures a pretix instance.


## Requirements

It needs an apt based system like Debian or Ubuntu. Also the [stuvusIT.nginx](https://github.com/stuvusIT/nginx), [stuvusIT.redis](https://github.com/stuvusIT/redis) and [stuvusIT.postgresql](https://github.com/stuvusIT/postgresql) roles are required.








## Role Variables

| Name                                | Required                 | Default                                                                     | Description                                                                                                                                                                                     |
|:------------------------------------|:------------------------:|:----------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pretix_instance_name`                 | :heavy_check_mark:       |                                                                             | Name of the Pretix Installation |
| `pretix_instance_url`                  | :heavy_check_mark:       |                                                                             | Full URL of the installation without trailing Slash |
| `pretix_mail_address`                  | :heavy_check_mark:       |                                                                             | Name of the mail address, pretix will send mails from |
| `pretix_mail_username`                 | :heavy_check_mark:       |                                                                             | Username of mail account to send mails from |
| `pretix_mail_password`                 | :heavy_check_mark:       |                                                                             | Password of mail account to send mails from |
| `pretix_mail_host`                     | :heavy_multiplication_x:       |  `127.0.0.1`                                                          | Full URL of the installation without trailing Slash |
| `pretix_mail_port`                     | :heavy_multiplication_x:       |  `465`                                                                | Name of the mail address, pretix will send mails from |
| `pretix_mail_tls`                      | :heavy_multiplication_x:       |  `on`                                                                 | "on" if you want to authenticata smtp with TLS |
| `pretix_user`                      | :heavy_multiplication_x:       |  `pretix`                                                                 | Name of the User pretix will be installed under |
| `pretix_group`                     | :heavy_multiplication_x:       |  `pretix`                                                                 | Name of the Usergroup pretix will be installed under |
| `pretix_config_dor`              | :heavy_multiplication_x:       |  `/etc/pretix`                                                               | Path of the pretix config |
| `pretix_dir`                     | :heavy_multiplication_x:       |  `/var/pretix`                                                               | Path where alle files will be stored |
| `pretix_db_name`                     | :heavy_multiplication_x:       |  `pretix`                                                                 | Name of the Pretix DB (Postgres) |
| `pretix_db_user`                     | :heavy_multiplication_x:       |  `pretix`                                                                 | Name of the Pretix DB User (Postgres) |
| `pretix_redis_location`                     | :heavy_multiplication_x:       |  `127.0.0.1`                                                       | Host of the redis installation |



## Example Playbook

```yml
- hosts: all
  become: true
  vars:
    pretix_instance_name: Stuvus Tickettest
    pretix_instance_url: tickets.int.stuvus.uni-stuttgart.de

    pretix_mail_name: stuvus - Tickets
    pretix_mail_address: tickets@stuvus.uni-stuttgart.de
    pretix_mail_username: ttickets
    pretix_mail_password:  dasjkdajskdjaskjd
    pretix_mail_host: mail01.faveve.uni-stuttgart.de

    # Postgres
    postgres_initdb: /usr/lib/postgresql/9.6/bin/initdb


    
    # Nginx
    served_domains:
      - domains:
          - tickets
        privkey_path: /etc/ssl/privkey.pem
        fullchain_path: /etc/ssl/fullchain.pem
        default_server: true
        crypto: true
        https: false

        locations:
        - condition: /
          content: |
            proxy_pass http://localhost:8345/;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
            proxy_set_header Host $http_host;
        - condition: /media/
          content: |
            alias /var/pretix/data/media/;
            expires 7d;
           access_log off;
        - condition: ^~ /media/cachedfiles 
          content: |
            deny all;
            return 404;
        - condition: ^~ /media/invoices 
          content: |
            deny all;
            return 404;
        - condition: /static/ 
          content: |
            alias /var/pretix/venv/lib/python3.5/site-packages/pretix/static.dist/;
            access_log off;
            expires 365d;
            add_header Cache-Control "public";



  roles:
    - redis
    - postgres
    - nginx
    - pretix
```

## License

This work is licensed under a MIT License.


## Author Information

- [Christopher Behrmann (astrocbxy)](https://github.com/astrocbxy) _chris.behrmann@stuvus.uni-stuttgart.de_
