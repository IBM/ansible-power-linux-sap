## Create or use an existing VPC VirtualServer as monitoring host

The Ansible playbook has to be executed from a IBM Cloud VPC VirtualServer
in  the same region and the same Security Group as your monitored SAP System(s).


Create a new or use an existing VPC VirtualServer as monitoring host
"SUSE Linux Enterprise Server 15 SP5 for SAP Applications amd64"
with the same security group as the SAP System.
The network architecture of a SAP System on Power is described in this [IBM Cloud Document](https://cloud.ibm.com/docs/sap-powervs?topic=sap-powervs-sap-ready-to-go)


Click on  IBM Cloud catalog: search “Virtual server for VPC “, click on the suggested Virtual server for VPC"
as described in this [IBM Cloud Document](https://cloud.ibm.com/docs/vpc?topic=vpc-creating-virtual-servers&interface=ui)

Choose these settings for the new host:

- Location: select the same region in which the existing SAP System is running
- As OS image select the SLES image "ibm-sles-15-5-amd64-sap-applications-2"
- As Profile choose the smallest available sizing (2 cores).
- Add your matching SSH keys as described in this [IBM Cloud Document](https://cloud.ibm.com/docs/vpc?topic=vpc-managing-ssh-keys&interface=ui)
- use a network with the same security group as the SAP System.
  Manage the local network of the host as described in this [IBM Cloud Document](https://cloud.ibm.com/docs/vpc?topic=vpc-using-instance-vnics&interface=ui).


  
