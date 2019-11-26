# Deploying OCI Streaming Service

## Table of Contents

[Overview](#overview)

[Pre-Requisites](#pre-requisites)

[Sign in to OCI Console and create your own compartment](#sign-in-to-oci-console-and-create-your-own-compartment)

[Create compute instance within your own compartment](#create-compute-instance-within-your-own-compartment)

[Download Script to configure Streaming service and Publish messages](#download-script-to-configure-streaming-service-and-publish-messages)

## Overview

In this lab we will create a compute instance, which will be used for other labs, as well during this 3 days training. As our request for service limit increase didn't go through yet, we will use qloudable for the Streaming lab, but still you need to create this training instance for other labs, so please continue with this guide first.

**Some Key points:**

**We recommend using Chrome or Edge as the broswer. Also set your browser zoom to 80%**


- All screen shots are examples ONLY. Screen shots can be enlarged by Clicking on them

- Everyone should use his own SSO account.

- SSH key pair will be provided for your convenience, but you may create your own.

- Do NOT use compartment name and other data from screen shots. Only use data (including compartment name) provided in the content section of the lab

- Mac OS Users should use ctrl+C / ctrl+V to copy and paste inside the OCI Console


**Note:** OCI UI is being updated thus some screenshots in the instructions might be different than actual UI

## Pre-Requisites

1. OCI Training : https://cloud.oracle.com/en_US/iaas/training

2. Familiarity with OCI console: https://docs.us-phoenix-1.oraclecloud.com/Content/GSG/Concepts/console.htm

3. Overview of Networking: https://docs.us-phoenix-1.oraclecloud.com/Content/Network/Concepts/overview.htm

4. Familiarity with Compartment: https://docs.us-phoenix-1.oraclecloud.com/Content/GSG/Concepts/concepts.htm

5. Connecting to a compute instance: https://docs.us-phoenix-1.oraclecloud.com/Content/Compute/Tasks/accessinginstance.htm

## Sign in to OCI Console and create your own compartment

**Note:** OCI UI is being updated thus some screenshots in the instructions might be different than actual UI

1. [Sign in](https://console.eu-frankfurt-1.oraclecloud.com/a/compute/instances?tenant=oraseemeaceeociworkshop&provider=OracleIdentityCloudService&_adf.ctrl-state=undefined&_afrLoop=undefined) using your SSO account to the oraseemeaceeociworkshop tenant.

2. From OCI servies menu, Click **Identity** under **Compartments**

3. Click on **Create Compartment** button

4. Give a name to your compartment and select **Workshops** as the parent compartment. You will use this compartment throughout this training.

5. Click **Create Compartment**. 

<img src="https://github.com/lsarecz/learning-library/blob/master/oci-library/qloudable/Deploying_OCI_Streaming_service/img/Compartment.png" alt="image-alt-text">

## Create compute instance within your own compartment

1. From OCI servies menu, Click **Instances** under **Compute**

2. Select the compartment you just created (within Workshops compartment) from drop down menu on left part of the screen

3. Click **Create Instance**. Fill out the dialog box:


- **Name:** Enter a name
- **Image Operating System:** Click **Change Image Source**. In the new window, Click **Oracle Images** Choose **Oracle Cloud Developer Image**. Scroll down, Accept the Agreement and Click **Select Image**
- **Availability Domain:** Select availability domain

<img src="https://raw.githubusercontent.com/oracle/learning-library/master/oci-library/qloudable/Deploying_OCI_Streaming_service/img/Stream_009.PNG" alt="image-alt-text">


- **Choose Instance Type:** Select Virtual Machine
- **Choose Instance Shape:** Select VM shape (Choose from VM.Standard2.1, VM.Standard.E2.1, VM.Standard1.1, VM.Standard.B1.1)
- **Virtual Cloud Network Compartment:** Choose your own compartment
- **New Virtual Cloud Network:** As we didn't create a VCN yet in this compartment, this wizard will create one for you. You may leave the default name or you can give it a name if you like 
- **Assign a public IP address:** Check this option
- **Boot Volume:** Leave the default values
- **Add SSH Keys:** Choose 'Paste SSH Keys' and paste the Public Key the instructors provided (or you may use your own).

4. Click **Create**

<img src="https://raw.githubusercontent.com/oracle/learning-library/master/oci-library/qloudable/OCI_Quick_Start/img/RESERVEDIP_HOL0011.PNG" alt="image-alt-text">

5. Wait for Instance to be in **Running** state. In git-bash Enter Command:
```
 cd .ssh
```
If you don't have .ssh folder yet, create it first:
```
 mkdir .ssh
 cd .ssh
```
6. Enter **ls** and verify the right id_rsa file exists. If not, copy the file you got from the instructors

7. Enter command 
```
ssh -i id_rsa opc@<PUBLIC_IP_OF_COMPUTE>
```

**HINT:** If 'Permission denied error' is seen, ensure you are using '-i' in the ssh command

8. Enter 'Yes' when prompted for security message

<img src="https://raw.githubusercontent.com/oracle/learning-library/master/oci-library/qloudable/OCI_Quick_Start/img/RESERVEDIP_HOL0014.PNG" alt="image-alt-text">
 
9. Verify opc@<COMPUTE_INSTANCE_NAME> appears on the prompt

## Download Script to configure Streaming service and Publish messages

1. First we have to configure OCI CLI in ssh session to compute instance. You may want to use your existing private key, if you have an API Key already created. If you don't have an API Key yet, you can skip this step. Otherwise create two files and copy the content of your key pair:
```
mkdir .oci
cd .oci
nano oci_api_key.pem
nano oci_api_key_public.pem
```
Copy&Paste your private key file content.

Update the permissions of the private key file:
```
chmod 600 oci_api_key.pem
cd ..
```
2. Now you can configure OCI CLI, Enter command:

```
oci setup config
```

3. Accept the default directory location. For user OCI switch to OCI Console window. Click Human Icon and then your user name. In the user details page Click **copy** to copy the OCID. **Also note down your region name as shown in OCI Console window**. Paste the OCID in ssh session.

<img src="https://raw.githubusercontent.com/oracle/learning-library/master/oci-library/qloudable/Deploying_OCI_Streaming_service/img/Stream_004.PNG" alt="image-alt-text">

4. Repeat the step to find tenancy OCID (Human icon followed by Clicking Tenancy Name). Paste the Tenancy OCID in ssh session to compute instance followed by providing your region name (eu-frankfurt-1, uk-london-1 etc)

5. When asked for **Do you want to generate a new RSA key pair?** answer Y, if you didn't copy your existing key in step 1. Otherwise answer N. For the rest of the question accept default by pressing Enter

<img src="https://raw.githubusercontent.com/oracle/learning-library/master/oci-library/qloudable/Deploying_OCI_Streaming_service/img/Stream_005.PNG" alt="image-alt-text">

6. If **oci setup config** generated an API key, you will need to upload this API key into your OCI account for authentication of API calls. Switch to ssh session to compute instance, to display the conent of API key Enter command:

```
cat ~/.oci/oci_api_key_public.pem
```

7. Hightlight and copy the content from ssh session. Switch to OCI Console, Click Human icon followed by your user name. In user details page Click **Add Public Key**. In the dialog box paste the public key content and Click **Add**.

<img src="https://raw.githubusercontent.com/oracle/learning-library/master/oci-library/qloudable/Deploying_OCI_Streaming_service/img/Stream_006.PNG" alt="image-alt-text">

<img src="https://raw.githubusercontent.com/oracle/learning-library/master/oci-library/qloudable/Deploying_OCI_Streaming_service/img/Stream_007.PNG" alt="image-alt-text">

8. Download and Install pip utility which will be used to install additional software. Enter command:

```
sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

followed by

```
sudo python get-pip.py

```

9. Install a virtual environment. This is being done so we have a clean enviornment to execute our python script that will create and publish messages to OCI streaming service. Enter command:

```
sudo pip install virtualenv
```

***Congratulations! You have successfully completed the lab. ***

