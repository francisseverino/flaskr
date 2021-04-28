Vagrant.configure("2") do |config|

    config.vm.box = "bento/ubuntu-18.04"
    config.vm.network "forwarded_port", guest: 80, host: 8000

    config.vm.provision "shell", inline: <<-SHELL
      #Install needed packages
      apt-get update
      apt-get upgrade
      apt-get install -y python3-venv nginx git

      #Clone repository
      git clone https://github.com/francisseverino/flaskr.git
  
      #Vagrant owner
      chown vagrant:vagrant -R flaskr
  
      # Copy app.service and nginx.conf to their folders
      cp flaskr/app.service /etc/systemd/system/
      cp flaskr/nginx.conf /etc/nginx/
      nginx -s reload
    SHELL
  
    config.vm.provision "shell", privileged: false, inline: <<-SHELL
      # Create virtual env and install requirements
      cd flaskr
      python3 -m venv .venv
      .venv/bin/pip install -e .

      # Initialize db
      export FLASK_APP=flaskr
      .venv/bin/flask init-db
    SHELL
  
    # Start at boot
    config.vm.provision "shell", inline: <<-SHELL
      systemctl enable app
      systemctl start app
  SHELL
  end