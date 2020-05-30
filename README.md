# k8s-audit-log-inspector

Want to study how k8s audit logs work? This repo provides a Vagrant box configuration that:

1. Sets up microk8s with audit logging configured
2. Loads a custom audit policy
3. Sets up Elasticsearch and Kibana to ship logs to
4. Sets up filebeat to watch the microk8s audit logs and ship them to Elastic
5. Opens up port 5601 on localhost so that you can navigate to the logs in your browser

To run, simply [install Vagrant][2] and then run `vagrant up` in the root of the repo.

To modify the audit policy, edit `kube-api-audit-policy` as required, and then
run `vagrant reload --provision`.

This box takes several minutes to build due to the size of several of the images.
If you have a beefy machine I also recommend editing the `v.memory` and `v.cpus`
values at the top of the Vagrantfile, as Elasticsearch and Kibana will generally
take all the memory they can get.

Ideally we would be able to use something like [kind][1], but it does not currently support
audit logging, so we use the Vagrant approach instead.

[1]: https://github.com/kubernetes-sigs/kind
[2]: https://www.vagrantup.com/docs/installation
