sa-traefik
===========
[![Build Status](https://travis-ci.org/softasap/sa-traefik.svg?branch=master)](https://travis-ci.org/softasap/sa-traefik)

Note: play relies on changes to be introduced in traefik since 1.4.0 ;
Previous traefik versions are supported via `option_confd_fallback` option

Example of use: check box-example

Simple:

```YAML

...

roles:

     - {
         role: "sa-traefik",
         option_confd_fallback: true
       }

```


Advanced:


```YAML


     - {
         role: "sa-traefik",
         option_confd_fallback: true

         traefik_install_dir: /opt/traefik,
         traefik_version: "1.4.0-rc1",
        
         option_traefik_conf_docker: true,
         option_traefik_conf_defaultentrypoints: true,
         option_traefik_conf_webbackend: true,

         traefik_settings:
           - {
             name: "logLevel",
             value: "DEBUG"
             }
           - {
             name: "traefikLogsFile",
             value: "/var/log/traefik.log"
             },

         traefik_extra_settings: []

       }

```

Usage notes
-----------

In difference from other `traefik` roles, using "naked" setup, you will not get UI.

Instead, we expect, that your server specific configuration will be placed into conf.d/ folder
on a later stages.

files under conf.d are either supposed to be handled natively by traefik (since 1.4.0, most likely),
or they will be merged into generated config in alphabetical order , if `option_confd_fallback` is set.

Typical example of the application specific configuration play is provided below.

```
- name: Traefik | Configure default entry points
  template: src="{{playbook_dir}}/templates/entrypoints.toml.j2" dest="/opt/traefik/conf.d/entrypoints.toml" owner="root" group="root" mode="0644"
  become: yes
  tags:
    - traefik
    - create

- name: Traefik | Configure web administration backend
  template: src="{{playbook_dir}}/templates/web_backend.toml.j2" dest="/opt/traefik/conf.d/web_backend.toml" owner="root" group="root" mode="0644"
  become: yes
  tags:
    - traefik
    - create

- name: Traefik | Configure docker link
  template: src="{{playbook_dir}}/templates/docker.toml.j2" dest="/opt/traefik/conf.d/docker.toml" owner="root" group="root" mode="0644"
  become: yes
  tags:
    - traefik
    - create


- name: Traefik | Restart if watching is not activated
  service: name="traefik" state="restarted" enabled="yes"
  become: yes
  tags:
    - traefik
    - create
```


Usage with ansible galaxy workflow
----------------------------------

If you installed the sa-traefik  role using the command


`
   ansible-galaxy install softasap.sa-traefik
`

the role will be available in the folder library/sa-traefik

Please adjust the path accordingly.

```YAML

     - {
         role: "softasap.sa-traefik"
       }

```



Copyright and license
---------------------

Code licensed under the [BSD 3 clause] (https://opensource.org/licenses/BSD-3-Clause) or the [MIT License] (http://opensource.org/licenses/MIT).

Subscribe for roles updates at [FB] (https://www.facebook.com/SoftAsap/)

Join Gitter channel at [Gitter] (https://gitter.im/softasap/)
