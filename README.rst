=========
Ims Setup
=========

It is the setup of an IMS core and once the whole setup is complete using SIP clients like Zoiper calls can be made using numbers provided by this IMS deployment

* The whole setup consists of::
    
    $ 5 IMS core VNFs
    $ 2 Zoiper clients
    $ 1 Puppet master VM for installing packages on the core VNFs of this setup


Steps carried out in each IMS core VNF for its own configuration
-----------------------------------------------------------------

* Puppet Specific::

    $ Primary values are written in the Clearwater config file (/etc/clearwater/config)
    $ All the debian packages specific to the node are copied and installed (all .deb file along with their dependencies)
    $ ssh, netconfd and bono services are configured and restarted
    $ Openyuma server is installed on the node

* Driver Specific::

    $ Once the configuration is complete the vnfmanager will invoke the node specific driver 
    $ Driver uses ncclient to write configuration in the config file of that particular IMS core VNF
    $ All the IP related values are written to the config file at this stage by the vnfmanager fetching information from vnfsvc


Deploying the IMS core VNFs
----------------------------

* Installation steps::
    $ Verify if vnfsvc_examples folder was cloned. Folder should exist in local machine with matching contents.

    $ Update the nsd and vnfd templates(exists in vnfsvc_examples) path in /etc/vnfsvc/templates.json

    $ Update the  image(exists in sample_templates) paths in vnfd_ims.yaml for flavor "Silver" as indicated in the templates.

    $ Update the userdata(exists in vnfsvc_examples) path under deployment_artifact tag for "Silver" flavor in vnfd_vLB.yaml

    $ Update the image(any desktop image) and flavor details under 'my_instance' and 'my_instance1' tags in heat.yaml

    $ Upload the heat.yaml to HEAT. Enter the values given below for heat template attributes before uploading it.
         name   	- ims
         flavor 	- Silver
         private 	- 192.168.1.0/24 (Any IPv4 CIDR)
         mgmt-if 	- 192.168.3.0.24 (Any IPv4 CIDR)
         router 	- <Router name>

    $ According to the dependencies mentioned in the NSD templated the VNF's will be launched.

    $ After launching the non-dependent VNF/VNF's for a network service, VNFManager is created which takes care of configuring the VNF through managment network if required.

    $ Once the configuration of non-dependent VNF/VNF's are done, an acknowledgement will be sent to VNFSVC service.
      After receiving the acknowledgement by VNFSVC service, it will resolve the next level of dependencies and then deploy those.
      This will be repeated until all the mentioned VNFS are deployed.


Make your first call
---------------------

* Prerequisites::

    $ All the Clearwater nodes should be up and running
    $ Note down the Ellis and Bono IP address

* In the Zoiper VMs do the following steps::

    $ Navigate to the ellis url which is http://<ellis ip>
    $ Sign up as a new user, using the signup code as “secret” 
    $ Ellis will automatically allocate a new number(<sip username>)(Ex: 6505550001@test.com) and password (<password>)

    $ Open the Zoiper client -> Select settings -> preferences -> select create account -> Select account type as SIP -> Next
    $ Enter the credentials
	  user/user@host	= <sip username>
	  password		= <password>
	  Domain/outbound proxy = <bono ip>:5060

    $ Click Next and Select “skip auto detection” and click Next and close it.
    $ In preferences, 
          Select general and give the Auth.username as <sip username>
          Select Advanced , 
          select transport as TCP 
          Give the Stun options as below
          Server Hostname /IP= <bono ip>
          Port = 3478

    $ Once the setup is complete dial the SIP client number from one client and answer from the other client
