# virl-appcat
virl appcatalyst basics

This is after you install vmware appcatalyst and appcatalyst plugin

Clone this repo:

if you dont with to launch the demo topology
Edit Vagrantfile and comment out line below like the example below 
#config.vm.provision "shell", inline: $demolaunch, run: "always"


Edit virl.ini and replace:
  a. salt_master (if external us-1.virl.info,us-2.virl.info,us-3.virl.info,us-4.virl.info)
  b. salt_id: (to one specific to you)
  c. salt_domain: (to one specific to you)
  d. Alter image list if you would like to add additional images

Place the private key you received in salt/minion.pem 

vagrant up

First launch will download the box which will take awhile at 1.5G

Assuming your keys are valid it will:
 a. Install ank and std
 b. Install images
 c. Clone sample topology repo
 d. Launch test triangle simulation
 e. Provide live vis link at the end
