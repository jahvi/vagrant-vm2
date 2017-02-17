# Vagrant VM

Base local development config for Magento 2 projects

## LAMP Stack

- Apache 2.4
- PHP 7.0
- MySQL 5.7

## PHP Modules

- cli
- intl
- mcrypt
- gd
- curl
- mbstring
- zip

## Misc Modules

- XDebug
- Mailhog (accesible at servername:8025)

## Generate initial VM

This repo is meant to be used in conjunction with [PuPHPet](https://puphpet.com/), to generate the initial VM files go to the PuPHPet website and drag the `config.yaml` file into it, then download the archive with the config options and unpack it anywhere locally. This will become the root for all websites under development, before starting make sure to run `vagrant up` in this directory to provision the VM with all required packages.

Make sure the included `stdev` file is inside the VM `puphpet` folder before running `vagrant up`, this will enable automatic subdomain mappings.

## Extending config settings

The main [config file](https://github.com/jahvi/vagrant-vm2/blob/master/config.yaml) can be extended by creating create a `config-custom.yaml` file with the options that need changing. For example to change the VM id and hostname the `config-custom.yaml` file should look like this:

```
vagrantfile:
    vm:
        provider:
            local:
                machines:
                    vflm_vnuunqc5363d:
                        id: MyMachine
                        hostname: my-machine.dev
```

### Adding additional virtual hosts

The VM is configured to use wildcard subdomains which means that all folders inside the VM directory will be mapped to `foldername.medialounge2.dev` automatically.

Sometimes it's necessary to manually add virtual hosts (eg: multi store websites), for thse cases they can be added in the `config-custom.yaml` under the `apache` key. For example the following will create a new virtual host accessible at `project.dev` with the base directory named `project` and redirect all PHP request to the `index.php` file:

```
apache:
    vhosts:
        RandomUniqueString:
            servername: project.dev
            docroot: /var/www/project
            port: '80'
            setenvif:
                - 'Authorization "(.*)" HTTP_AUTHORIZATION=$1'
            custom_fragment: ''
            ssl: '0'
            ssl_cert: ''
            ssl_key: ''
            ssl_chain: ''
            ssl_certs_dir: ''
            ssl_protocol: ''
            ssl_cipher: ''
            directories:
                RandomUniqueString:
                    path: /var/www/project
                    options:
                        - Indexes
                        - FollowSymlinks
                        - MultiViews
                    allow_override:
                        - All
                    require:
                        - 'all granted'
                    custom_fragment: ''
                    files_match:
                        RandomUniqueString:
                            path: \.php$
                            sethandler: 'proxy:fcgi://127.0.0.1:9000'
                            custom_fragment: ''
                            provider: filesmatch
                    provider: directory
```

## Recommended workflow

1. Create a new directory for your project.
2. Make sure to add the domain URL to the `/etc/hosts` file and point it to the VMs IP.
