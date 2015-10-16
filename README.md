# virl-appcat
virl appcatalyst basics

This is after you install vmware appcatalyst and appcatalyst plugin

Clone this repo:
Edit Vagrantfile and replace
/Volumes/Data/images/salt with local directory for routervm storage

Edit virl.ini and replace:
salt_master (if external us-1.virl.info,us-2.virl.info,us-3.virl.info,us-4.virl.info)
salt_id: (to one specific to you)
salt_domain: (to one specific to you)

Image list if you would like additional images

Replace minion.pem and minion.pub with your own working keys.  (Or if internal just remove them)

vagrant up
You will download the box which will take awhile at 1.5G
Assuming your keys are valid it will:
Install ank and std
Install images
Clone sample topology repo
Launch test triangle simulation
Provide live vis link at the end
