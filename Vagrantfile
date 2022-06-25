# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|
  config.vm.box = "bento/centos-6"
  config.vm.network "forwarded_port", guest: 5432, host: 5432
  config.vm.provider "virtualbox" do |vb|
    vb.name = "vagrant_centos7_pgsql"
    vb.memory = "1024"
  end
  config.ssh.insert_key = false
  config.vm.provision "shell", inline: <<-SHELL
    echo "Update system"
    yum update -y
    echo "Install PostgreSQL"
    cat << EOF > /etc/yum.repos.d/pgdg-84.repo
[pgdg84]
name=PostgreSQL 8.4 RPMs for RHEL/CentOS 6
baseurl=https://yum-archive.postgresql.org/8.4/redhat/rhel-6-x86_64
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-PGDG
EOF
#  here the instructions -- https://yum.postgresql.org/repopackages/ 
    yum -y install postgresql84-server
    echo "Configure and start PostgreSQL"
    /etc/init.d/postgresql-8.4 initdb
  # Configure network access
    echo "Configuring postgresql network access"
    echo 'listen_addresses = '"'"'*'"'" >> /var/lib/pgsql/11/data/postgresql.conf
    echo 'host all all 0.0.0.0/0 trust' >> /var/lib/pgsql/11/data/pg_hba.conf
    /etc/init.d/postgresql-8.4 start
    echo "Creating vagrant user and database"
    cd /
    echo "CREATE ROLE vagrant CREATEDB CREATEROLE LOGIN PASSWORD 'vagrant'" | sudo -u postgres psql -a -f -
    echo "CREATE DATABASE vagrant OWNER vagrant" | sudo -u postgres psql -a -f -
    echo "ALTER USER postgres WITH PASSWORD 'POSTGRES'" | sudo -u postgres psql -a -f -
    echo "Restart postgresql "
    /etc/init.d/postgresql-8.4 restart
    echo "Done"
  SHELL
  config.vm.post_up_message = "Machine started"
end
