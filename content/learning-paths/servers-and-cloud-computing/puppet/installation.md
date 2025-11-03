---
title: Install Puppet
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install Puppet on GCP VM
This guide walks you through installing Puppet on a **Google Cloud Platform (GCP) SUSE Linux Arm64 VM**, including all dependencies, Ruby setup, and environment preparation.

### Install build dependencies and Ruby from source
Installs all required tools and builds Ruby 3.1.4 from source to ensure compatibility with Puppet.

```console
sudo zypper install -y gcc make openssl-devel libyaml-devel zlib-devel readline-devel gdbm-devel ncurses-devel
cd /usr/local/src
sudo wget https://cache.ruby-lang.org/pub/ruby/3.1/ruby-3.1.4.tar.gz
sudo tar -xzf ruby-3.1.4.tar.gz
cd ruby-3.1.4
sudo ./configure
sudo make && sudo make install
```

### Verify Ruby
Checks that Ruby is correctly installed and available in your system path.

```console
ruby -v   
which ruby
```

```output
ruby 3.1.4p223 (2023-03-30 revision 957bb7cb81) [aarch64-linux]
/usr/local/bin/ruby
```

### Install Puppet dependencies
Installs essential Puppet libraries (`semantic_puppet, facter, hiera`) needed for automation tasks.

```console
wget https://github.com/puppetlabs/puppet/archive/refs/tags/8.10.0.tar.gz
tar -xvf 8.10.0.tar.gz
cd ~/puppet-8.10.0
sudo /usr/local/bin/gem install semantic_puppet -v "~> 1.0"
sudo gem install facter -v "~> 4.0"
sudo gem install hiera
```

### Build and install the Puppet gem
Builds and installs the Puppet 8.10.0 package from source into your Ruby environment.

```console
sudo gem build puppet.gemspec
sudo /usr/local/bin/gem install puppet-8.10.0.gem
```

### Verification
Confirms Puppet is successfully installed and ready to use on the system.

```console
puppet --version
```

Output:
```output
8.10.0
```
