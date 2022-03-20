# Waston-ML-n-Studio-Installation

Using IBM Watson Machine Learning, you can build analytical models and neural networks, trained with your own data, that you can deploy for use in applications. Watson Machine Learning provides a full range of tools and services so you can build, train, and deploy Machine Learning models. Choose from tools that fully automate the training process for rapid prototyping to tools that give you complete control to create a model that matches your needs.

Watson Studio provides the environment and tools for you to collaboratively work on data to solve your business problems. You can choose the tools you need to analyze and visualize data, to cleanse and shape data, to ingest streaming data, or to create and train machine learning models. The architecture of Watson Studio is centered around the analytics project. Data scientists and business analysts use analytics projects to organize resources and analyze data. This illustration shows the organization and interactions of an analytics project for Watson Studio.

This article documented step-by-step instructions to deploy IBM Watson ML and Studio on IBM Cloud Pak for Data running on Red Hat OpenShift in Azure.

# Assumptions
 - Red Hat OpenShift cluster has access to a high-speed internet connection and can pull images directly from IBM Entitled Registry. If not set up yet, please follow the instructions provided here.
 - IBM Cloud Pak for data Control Plane, Foundational Services are installed and running. If not, please follow the instructions provided here.
 - IBM Cloud Pak for data operator is installed in the “ibm-common-services” namespace, and foundational services are installed in the “cpd-instance” namespace.
 - WML & Studio operator will be installed in the “ibm-common-services” namespace, and the WML and Studio service will be installed in the “cpd-instance” namespace.
 - Installing for demo purposes and so, the latest version of the software will automatically install on the Red Hat OpenShift cluster.
User has knowledge and experience managing Red Hat OpenShift cluster

# Pre-Requisite
 - Red Hat OpenShift cluster version 4.6 or later with min 48 vCPU and 196 GB RAM
 - Bastion host with two vCPU and 4GB RAM with Linux OS
 - Internet access for Bastion host and Red Hat OpenShift cluster
 - OpenShift Container Storage (OCS) attached to Red Hat OpenShift cluster. This link will help you determine supported storage. In this demo, I have used OCS Storage.
 - A User with OpenShift Cluster and Project Administrator access

# Step 1: Download files from the GitHub repo using the following command.

        git clone https://github.com/kapilrajyaguru/Data-Virtualization.git

  After downloading files, switch to the Watson-Knowledge-Catalog-Installation directory.
        
        cd Data-Virtualization/

# Step 2 - Create the Watson Machine Learning operator subscription.
  
        oc apply -f wmloperator.yaml
 
 - Validate that the operator was successfully created.
   Run the following command to confirm that the subscription was triggered:

        oc get sub -n ibm-common-services ibm-cpd-wml-operator-subscription -o jsonpath='{.status.installedCSV} {"\n"}'

   Verify that the command returns ibm-cpd-wml-operator.v1.1.5.

 - Run the following command to confirm that the cluster service version (CSV) is ready:

        oc get csv -n ibm-common-services ibm-cpd-wml-operator.v1.1.5 -o jsonpath='{ .status.phase } : { .status.message} {"\n"}'

   Verify that the command returns Succeeded : install strategy completed with no errors.

 - Run the following command to confirm that the operator is ready:

        oc get deployments -n ibm-common-services -l olm.owner="ibm-cpd-wml-operator.v1.1.5" -o jsonpath="{.items[0].status.availableReplicas} {'\n'}"

   Verify that the command returns an integer greater than or equal to 1. If the command returns 0, wait for the deployment to become available.
   
# Step 3 - Create a WmlBase custom resource to install Watson Machine Learning. 

       oc apply -f wml.yaml
  
  - To check whether the WML has finished installing Watson Machine Learning service pods, run the following command:

		   oc get WmlBase wml-cr -o jsonpath='{.status.wmlStatus} {"\n"}'
        
   Watson Machine Learning is ready when the command returns Completed
   
# Step 4 - Create the Watson Studio operator subscription.

       oc apply -f studio_sub.yaml
        
 - Validate that the operator was successfully created.
   Run the following command to confirm that the subscription was triggered:

       oc get sub -n ibm-common-services ibm-cpd-ws-operator-catalog-subscription -o jsonpath='{.status.installedCSV} {"\n"}'

    Verify that the command returns ibm-cpd-wsl.v2.0.6.

 - Run the following command to confirm that the cluster service version (CSV) is ready:

       oc get csv -n ibm-common-services ibm-cpd-wsl.v2.0.6 -o jsonpath='{ .status.phase } : { .status.message} {"\n"}'

   Verify that the command returns Succeeded : install strategy completed with no errors.

 - Run the following command to confirm that the operator is ready:

       oc get deployments -n ibm-common-services -l olm.owner="ibm-cpd-wsl.v2.0.6" -o jsonpath="{.items[0].status.availableReplicas} {'\n'}"

   Verify that the command returns an integer greater than or equal to 1. If the command returns 0, wait for the deployment to become available.
   
# Step 5 - Create a Watson Studio custom resource to install Watson Studio. 

       oc apply -f ws.yaml
  
  - To check whether the Watson Studio has finished installing Watson studio service pods, run the following command:

		   oc get WS ws-cr -o jsonpath='{.status.wsStatus} {"\n"}'
        
   Watson Studio is ready when the command returns Completed
