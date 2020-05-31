Vagrant.configure("2") do |config|

  # Elasticsearch is painfully slow when starting up without this
  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 2
  end

  # Ubuntu base box for easy microk8s installation
  config.vm.box = "hashicorp/bionic64"

  # Define microk8s audit configuration
  config.vm.provision "file", source: "./audit-config",
                              destination: "/home/vagrant/audit-config"

  # Install microk8s and configures audit logging
  config.vm.provision :shell, inline: "snap install microk8s --classic && \
                                       cat audit-config >> /var/snap/microk8s/current/args/kube-apiserver"

  # Load the audit policy into the VM, restart microk8s to pick up changes
  # TODO: Use a synced folder (?)
  config.vm.provision "file", source: "./kube-api-audit-policy",
                              destination: "/tmp/kube-api-audit-policy"
  config.vm.provision "shell", inline: "mv /tmp/kube-api-audit-policy /var/snap/microk8s/current/args/"
  config.vm.provision "shell", inline: "systemctl restart snap.microk8s.daemon-apiserver.service"

  # Loads the filebeat config into the VM
  config.vm.provision "file", source: "./filebeat.docker.yml",
                              destination: "/home/vagrant/filebeat.docker.yml"

  # Elasticsearch, Kibana, and Filebeat all run as Docker images; set them up here
  config.vm.provision "docker" do |d|

    # Create a bridged network for all the containers to use
    # Use `|| true` to succeed even if the audit network already exists
    d.post_install_provision "shell", inline:"docker network create --driver bridge audit || true"

    d.run "elasticsearch:7.7.0",
                args: "--name elasticsearch \
                       -p 9200:9200 -p 9300:9300 \
                       --network audit \
                       -e 'discovery.type=single-node'"

    d.run "kibana:7.7.0", args: "--name kibana \
                                 -p 5601:5601 \
                                 --network audit"

    d.run "docker.elastic.co/beats/filebeat:7.7.0 -e -strict.perms=false",
                args: "--name=filebeat \
                       --network audit \
                       --volume='/home/vagrant/filebeat.docker.yml:/usr/share/filebeat/filebeat.yml' \
                       --volume='/var/log:/tmp/host-logs/'"
  end

  # Open a port to Kibana on the host machine
  config.vm.network "forwarded_port", guest: 5601, host: 5601

end
