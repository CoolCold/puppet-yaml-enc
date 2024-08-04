# YAML-based Puppet ENC

This is a simple Puppet ENC (External Node Classifier) that classifies
nodes based on regular expressions read from a YAML file.

## Configuration File Format

The configuration file is a YAML dictionary where each key represents a host
regular expression. If no match is found, attributes from the (optional) host
_DEFAULT_ is returned.

The following attributes are allowed by Puppet:

- environment
- classes
- parameters

At least _environment_ or _classes_ must be defined.


## Configuration Example

    DEFAULT:
      classes: []
    
    '^foo-dev-bar\d+\..+$':
      environment: develop
      parameters:  { guild: foo-dev }
      classes: [ 'roles::foo::bar' ]
    
    '^foo-prod-bar\d+\..+$':
      environment: production
      parameters:  { guild: foo-prod }
      classes: [ 'roles::foo::bar' ]

# Installing
## script part
May be not the best or proper way, but at least starting point for those who doesn't deal with python every year.

on Ubuntu 22.04 (should work on 20.04 as well):
```
apt-get install python-is-python3
git clone ...
cd <cloned_dir>
python setup.py install
```
File gonna be installed into `/usr/local/bin/yamlenc`.

## puppet part
Edit (on Puppetmaster) `/etc/puppetlabs/puppet/puppet.conf`

Into `[server]` section, put:
```
node_terminus = exec
external_nodes = /usr/local/bin/yamlenc --conf /opt/puppet-enc/enc.yaml
```
Sample of full config `/etc/puppetlabs/puppet/puppet.conf`:

```
# This file can be used to override the default puppet settings.
# See the following links for more details on what settings are available:
# - https://puppet.com/docs/puppet/latest/config_important_settings.html
# - https://puppet.com/docs/puppet/latest/config_about_settings.html
# - https://puppet.com/docs/puppet/latest/config_file_main.html
# - https://puppet.com/docs/puppet/latest/configuration.html
[server]
vardir = /opt/puppetlabs/server/data/puppetserver
logdir = /var/log/puppetlabs/puppetserver
rundir = /var/run/puppetlabs/puppetserver
pidfile = /var/run/puppetlabs/puppetserver/puppetserver.pid
codedir = /etc/puppetlabs/code

node_terminus = exec
external_nodes = /usr/local/bin/yamlenc --conf /opt/puppet-enc/enc.yaml

[main]
server = prod-puppetmaster

[master]
storeconfigs = true
storeconfigs_backend = puppetdb
```
