============================ 
OepnVNFManager installation
============================

* Installation steps to be followed::

    $ Verify if vnfsvc_examples folder was cloned. Folder should exist in local machine with matching contents.

    $ Update the nsd and vnfd templates(exists in vnfsvc_examples) path in /etc/vnfsvc/templates.json 

    $ Update the loadbalancer and webserver image(exists in sample_templates) paths in vnfd_vLB.yaml and vnfd_vAS.yaml for flavor "Silver" as indicated in the templates.

    $ Update the userdata(exists in vnfsvc_examples) path under deployment_artifact tag for a "Silver" flavor in vnfd_vLB.yaml 

    $ Update the image(any desktop image) and flavor details under user_instance tag in heat.yaml

    $ Upload the heat.yaml to HEAT.Enter the values given below for heat template attributes before uploading it.
         name - webservice
         flavor - Silver
         private - 192.168.1.0/24 (Any IPv4 CIDR)
         mgmt-if - 192.168.3.0.24 (Any IPv4 CIDR)
         user-nw - 192.168.4.0/24 (Any IPv4 CIDR)
         router - <Router name>

    $ According to the dependencies mentioned in the NSD templated the VNF's will be launched.

    $ After launching the non-dependent VNF/VNF's for a network service, VNFManager is created which takes care of configuring the VNF through managment network if required.

    $ Once the configuration of non-dependent VNF/VNF's are done, an acknowledgement will be sent to VNFSVC service. 
      After receiving the acknowledgement by VNFSVC service, it will resolve the next level of dependencies and then deploy those.
      This will be repeated until all the mentioned VNFS are deployed.

    $ Open the console of user-vm.

    $ Check whether IP has been configured properly in user-vm.

    $ Check the reachabilty of loadbalancer from user-vm. (ping <loadbalancer private-ip>)
      
    $ Execute curl http://<loadbalancer private-ip>:8080 , should be able to access the webpage in the Application server.
