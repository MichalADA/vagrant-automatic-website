# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|


  # Web serwer Machine 
  config.vm.define "web01" do |web01|
    web01.vm.box = "centos/9"
    web01.vm.provider "virtualbox" do |vb|
      vb.name="web01"
      vb.cpus = 2
      vb.memory = "2048"

# Przekierowanie portu dla NGINX (dostęp publiczny)
  web01.vm.network "forwarded_port", guest: 80, host: 8080

# Adres IP prywatny dla komunikacji w sieci lokalnej
  web01.vm.network "private_network", ip: "10.10.0.10"
#provisioning 
web01.vm.provision "shell", inline: <<-SHELL
  # Instalacja NGINX
  sudo dnf install -y nginx

  # Uruchomienie i włączenie NGINX
  sudo systemctl start nginx
  sudo systemctl enable nginx

  # Pobranie i rozpakowanie strony internetowej
  wget https://www.tooplate.com/zip-templates/2134_gotto_job.zip -O /tmp/website.zip
  sudo unzip /tmp/website.zip -d /tmp/website
  sudo cp -r /tmp/website/* /var/www/html/
  rm -rf /tmp/website/*
  sudo systemctl restart ngnix

  # ustawianie bezpieczenstwa maszyny 
  sudo dnf install -y firewalld
  sudo systemctl start firewalld
  
  #otwieram porty dla http i https
  sudo firewall-cmd --permanet -add-service=http
  sudo firewall-cmd --permanet -add-service=https

  #  Ograniczenie liczby połączeń przychodzących z jednego adresu IP
# sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" 
    #source address="x.x.x.x/24" - tu mozna blokowac adresy limit value="10/m" accept'
  sudo firewall-cmd --reload  

end
 #konfiguracja db01 - serwera bazodanowego 
 
  config.vm.define "db01" do |db01|
  db01.vm.box = "centos/9"
  db01.vm.provider "virtualbox" do |vb|
    vb.name = "db01"
    vb.cpus = 2
    vb.memory = "2048"
  end

  # Adres IP prywatny dla komunikacji wewnętrznej
  db01.vm.network "private_network", ip: "10.10.0.11"

  # Tu dodamy skrypt provisioningowy
  db01.vm.provision "shell", inline: <<-SHELL
  # Instalacja MariaDB
  sudo dnf install -y mariadb-server

  # Uruchomienie i włączenie MariaDB
  sudo systemctl start mariadb
  sudo systemctl enable mariadb

  # Podstawowa konfiguracja bezpieczeństwa
  #sudo mysql_secure_installation to jakbysmy chcieli instalowac to bezpieczniej 
  # tu konfiguracja bazy danych 
  mysql -e "CREATE USER 'admin'@'%' IDENTIFIED BY 'minda';"
  mysql -e "GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';"
  mysql -e "FLUSH PRIVILEGES;"
  mysql -e "CREATE TABLE STRONA"
  sudo systemctl restart mariadb
  # Konfiguracja firewalld
  sudo systemctl start firewalld
  sudo systemctl enable firewalld
  sudo firewall-cmd --permanent --zone=public --add-port=3306/tcp
  sudo firewall-cmd --reload  

SHELL

  
end
# maszyna z rabbitmq

config.vm.define "rabbit" do |rabbit|
  rabbit.vm.box = "centos/9"
  rabbit.vm.provider "virtualbox" do |vb|
    vb.name = "rabbit"
    vb.cpus = 2
    vb.memory = "2048"
  end
  rabbit.vm.network "private_network", ip: "10.10.0.12"
  rabbit.vm.provision "shell", inline: <<-SHELL
  # Instalacja RabbitMQ
  sudo dnf install -y rabbitmq-server

  # Uruchomienie i włączenie RabbitMQ
  sudo systemctl start rabbitmq-server
  sudo systemctl enable rabbitmq-server

  # Opcjonalnie: Utworzenie użytkownika i konfiguracja wirtualnej kolejki
  # sudo rabbitmqctl add_user myuser mypassword
  # sudo rabbitmqctl set_permissions -p / myuser ".*" ".*" ".*"
SHELL
end
# maszyna do backupu 
config.vm.define "backup" do |backup|
  backup.vm.box = "centos/9"
  backup.vm.provider "virtualbox" do |vb|
    vb.name = "backup"
    vb.cpus = 1
    vb.memory = "1024"
  end
  backup.vm.network "private_network", ip: "10.10.0.13"
  # Skrypt provisioningowy
  backup.vm.provision "shell", inline: <<-SHELL
  # Instalacja narzędzi backupowych, np. rsync
  sudo dnf install -y rsync

  # Skrypt backupu (przykładowy)
  # sudo rsync -avz [ścieżka_do_katalogu_z_danymi] [ścieżka_do_katalogu_backup]
  # tu skrypt do backupu 

  # Konfiguracja harmonogramu backupu z użyciem cron (opcjonalnie)
  # echo "0 2 * * * /path/to/backup_script.sh" | crontab -
SHELL

end



end
