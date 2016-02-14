# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'vmware_appcatalyst'
#ENV['VAGRANT_DEFAULT_PROVIDER'] = 'vmware_fusion'

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.

config.vm.box = 'virl/unleaded'

#This is in the Vagrantfile packaged with our built boxes
#config.ssh.username = 'virl'

#expand timeout to deal with sims shutting down althought doesnt appear to work well now with appcat plugin
config.vm.graceful_halt_timeout = '90'

config.vm.post_up_message = "Welcome to virl appcatalyst demo box
We will automatically launch a 3 node iosv topology for you to interact with
simply cut and paste the above string into a browser.
Link to the accompanying lab is at
http://web.virl.info/nothing_to_see_here_yet.html"

#These doesnt work yet with appcatalyst..

config.vm.network "forwarded_port", guest: 19399, host: 20399
config.vm.network "forwarded_port", guest: 19400, host: 20400
config.vm.network "forwarded_port", guest: 19401, host: 20401
config.vm.network "forwarded_port", guest: 19402, host: 20402


# Pick a local directory that works for you to store router vms across boxes
config.vm.synced_folder "./images/", "/var/local/salt/", create: true

config.vm.provider "vmware_appcatalyst" do |v, override|
  v.vmx["memsize"] = "8096"
  v.memory = "8096"
  v.vmx["svga.vgaonly"] = "true"
  v.vmx["vmotion.checkpointSVGAPrimarySize"] = "0"
  v.vmx["numvcpus"] = "4"
  v.cpus = '4'
end
# config.vm.provider "vmware_fusion" do |v, override|
#   v.vmx["memsize"] = "8096"
#   v.memory = "8096"
#   v.vmx["svga.vgaonly"] = "true"
#   v.vmx["vmotion.checkpointSVGAPrimarySize"] = "0"
#   v.vmx["numvcpus"] = "4"
#   v.cpus = '4'
# end

config.vm.network "private_network", ip: "172.16.1.0", auto_config: false
config.vm.network "private_network", ip: "172.16.2.0", auto_config: false
config.vm.network "private_network", ip: "172.16.3.0", auto_config: false
config.vm.network "private_network", ip: "172.16.10.0", auto_config: false


$virlscript = <<SCRIPT
# You should place a virl.ini file in the same directory with your Vagrantfile
cp -f /vagrant/virl.ini /etc/virl.ini

if [ -d  /var/cache/salt/minion/files/base/images/salt ]; then
  rm -rf /var/cache/salt/minion/files/base/images/salt
fi
if [ ! -d  /var/cache/salt/minion/files/base/images ]; then
  mkdir -p /var/cache/salt/minion/files/base/images
fi

#Link to local copy of images
ln -s /var/local/salt /var/cache/salt/minion/files/base/images

#If your going to point/test externally you should just keep your keys here in your launch dir as well
cp -f /vagrant/*.pem /etc/salt/pki/minion
openssl rsa -in /vagrant/salt/minion.pem -outform PEM -pubout > /etc/salt/pki/minion/minion.pub

# set grains from your replacement ini file
/usr/local/bin/vinstall salt

echo 'Short pause for salt to recover'
sleep 5

# openstack fourth is a mashup of virl.openrc virl.std virl.ank
sudo salt-call -l quiet state.sls virl.vagrant

# Assuming you have put cached images in at this point pull the rest that you have specified
sudo salt-call -l quiet state.sls virl.routervms.all
SCRIPT

$simstopper = <<SCRIPT
if [ -f /var/local/virl/users/guest/endpoint/sessions/*.virl ]; then
   echo 'shutdown needed'
   /usr/local/bin/virl_std_client --quiet simengine-admin-stop --all-users --all-sessions
else
   echo 'shutdown not needed'
fi
SCRIPT

$demopulling = <<SCRIPT

# Add git repo dir and sample topository
if [ ! -d "/var/local/virl/users/guest/git/sample-topologies" ]; then
  sudo mkdir -p /var/local/virl/users/guest/git
  git clone -q --depth=1 https://github.com/VIRL-Open/sample-topologies.git /var/local/virl/users/guest/git/sample-topologies
else
  (cd /var/local/virl/users/guest/git/sample-topologies ; git pull)
fi
SCRIPT

$demolaunch = <<SCRIPT
#launch a specific virl file from the repo I just cloned in.
# Yes, if you already have the repo locally on your system you can just copy/share it into place as well
virl_std_client --quiet -u guest -p guest simengine-launch --virl-file /var/local/virl/users/guest/git/sample-topologies/Test_Topologies/3_node_test.virl

# spit out livevis link
/usr/local/bin/vislink
SCRIPT

config.vm.provision "shell", inline: $virlscript
config.vm.provision "shell", inline: $simstopper, run: "always"
config.vm.provision "shell", inline: $demopulling, run: "always"

#Comment out line below to prevent launch on up
config.vm.provision "shell", inline: $demolaunch, run: "always"


end
