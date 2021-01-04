# :detective: k8s-audit-log-inspector

Want to study how k8s audit logs work? This repo provides a Vagrant box configuration that:

1. Sets up microk8s with audit logging configured
2. Loads a custom audit policy
3. Sets up Elasticsearch and Kibana to ship logs to
4. Sets up filebeat to watch the microk8s audit logs and ship them to Elastic
5. Opens up port 5601 on localhost so that you can navigate to the logs in your browser

To run:

1. Install VirtualBox
2. [Install Vagrant][2]
3. Run `vagrant up` in the root of the repo

This box takes several minutes to build due to the size of several of the images.
If you have a beefy machine I also recommend editing the `v.memory` and `v.cpus`
values at the top of the Vagrantfile, as Elasticsearch and Kibana will generally
take all the memory they can get.

Once the build is finished, it might still take several minutes for Elasticsearch
and Kibana to set themselves up; I recommend waiting at least five minutes before
navigating to `localhost:5601` on the host machine.

Once the box is running, you can access the box via `vagrant ssh` to run operations
on the microk8s cluster. You will need to `sudo -i` to do so first (no password required)
as the default `vagrant` user currently isn't in the correct groups. Once you are root,
use `microk8s.kubectl` in order to run commands as usual, for example, `microk8s.kubectl get po`.

PRs are welcome for any fundamental issues with the default configuration. The build
has been tested on Ubuntu 18.04.

Ideally we would be able to use something like [kind][1], but it does not currently support
audit logging, so we use the Vagrant approach instead.

[1]: https://github.com/kubernetes-sigs/kind
[2]: https://www.vagrantup.com/docs/installation
