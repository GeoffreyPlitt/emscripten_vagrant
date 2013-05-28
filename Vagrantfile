def local_cache(box_name, purpose)
  cache_dir = File.join(File.expand_path(Vagrant::Environment::DEFAULT_HOME),
                        'cache',
                        purpose,
                        box_name)
  partial_dir = File.join(cache_dir, 'partial')
  FileUtils.mkdir_p(partial_dir) unless File.exists? partial_dir
  cache_dir
end

Vagrant.configure("2") do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.provision :shell, :inline => $BOOTSTRAP_SCRIPT # see below

  # apt package caching
  config.vm.synced_folder (local_cache config.vm.box, 'apt'), "/var/cache/apt/archives/"

end

$BOOTSTRAP_SCRIPT = <<EOF
  set -e # Stop on any error

  # --------------- SETTINGS ----------------
  # Other settings
  export DEBIAN_FRONTEND=noninteractive

  #echo 'deb http://archive.ubuntu.com/ubuntu/ raring main restricted universe multiverse' | sudo tee -a /etc/apt/sources.list
  sudo apt-get update
  sudo apt-get install -y debconf-utils
  # sudo debconf-set-selections <<< "libc6:amd64 libraries/restart-without-asking  boolean true"
  sudo apt-get install -y build-essential gcc g++ git-core curl nodejs openjdk-7-jre liballegro-dev
  cd ~vagrant && \
    sudo -u vagrant wget http://llvm.org/releases/3.2/clang+llvm-3.2-x86_64-linux-ubuntu-12.04.tar.gz && \
    sudo -u vagrant tar zxvf clang+llvm-3.2-x86_64-linux-ubuntu-12.04.tar.gz && \
  echo "PATH=/home/vagrant/clang+llvm-3.2-x86_64-linux-ubuntu-12.04/bin:$PATH" | sudo tee -a ~vagrant/.bashrc

  #emscripten
  cd ~vagrant && sudo -u vagrant git clone https://github.com/kripken/emscripten.git
  echo 'export PATH=~/emscripten:$PATH' | sudo tee -a ~vagrant/.bashrc

  cd ~vagrant && sudo -u vagrant git clone https://github.com/tjwei/xnes.git
  echo DONE.
EOF
