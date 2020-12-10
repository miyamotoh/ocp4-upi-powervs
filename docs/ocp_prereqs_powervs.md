# **PowerVS Prerequisites**
----------------------

## IBM Cloud Account
You'll need to have an IBM Cloud Account to be able to use Power Systems Virtual Server (PowerVS).

## Create Power Systems Virtual Server Service Instance

Login to [IBM Cloud Dashboard](https://cloud.ibm.com) and search for "**Power**" in the **Catalog**.
Select "**Power Systems Virtual Server**" and provide all the required inputs
to create the service instance.

<details>
  <summary>Screenshot for searching for "Power"</summary>

  ![Search for Power](./media/image1.png)
</details>

<details>
  <summary>Screenshot for selecting "Power Systems Virtual Server"</summary>

  ![Select Power Systems Virtual Server](./media/image2.png)
</details>

<details>
  <summary>Screenshot for filling out the Create form</summary>

  ![Fill Details](./media/image3.png)
</details>

1. Provide a meaningful name for your instance in the **Service name** field.
2. Select the proper **resource group**. More details on resource groups is available from the following [link](https://cloud.ibm.com/docs/account?topic=account-rgs)


## Create Private Network

A private network is required for your OCP cluster. Choose the previously created "**Service Instance**" and create a private subnet by selecting "**Subnets**" and providing the required inputs. If you see a screen displaying CRN and GUID, then click "View full details" to access the "Subnet" creation page.

You can create multiple OCP clusters in the same service instance using the same private network. If required you can also create multiple private networks.

<details>
  <summary>Provide the required inputs for private subnet creation</summary>

  ![Select subnet](./media/image6.png)
  ![Provide Input](./media/image7.png)
  ![Create subnet](./media/image8.png)
</details>

## Raise a Service Request to enable IP communication between PowerVS instances on private network
In order for your instances to communicate within the subnet, you'll need to create a service request.

<details>
  <summary>Click on <b>Support</b> in the top bar and scroll down to <b>Contact Support</b>, then select "<b>Create a case</b>"</summary>

  ![Create a case](./media/image9.png)
</details>

<details>
  <summary>Select "<b>Power Systems Virtual Server</b>" tile</summary>

  ![Create a case Page](./media/image10.png)
</details>

Complete the details as shown using the following template:

- [Subject:] Enable communication between PowerVS instances on private network
- [Body:]
  ```
    Please enable IP communication between PowerVS instances for the following private network:
    Name: <your-subnet-name-from-above>
    Type: Private
    CIDR: <your ip subnet-from-above>
    VLAN ID: <your-vlan-id> (listed in your subnet details post-creation)
    Location: <your-location> (listed in your subnet details post-creation)
    Service Instance: <your-service-name>
  ```

<details>
  <summary>Put the above in the form</summary>

  ![Sample support request ](./media/image11.png)
</details>

<details>
  <summary>Click "<b>Continue</b>" to accept agreements, and then Click "<b>Submit case</b>".</summary>

  ![Submit Case](./media/image12.png)
</details>


## RHCOS and RHEL 8.2 Images for OpenShift
RHEL image is used for bastion and RHCOS is used for the OpenShift cluster nodes.

You'll need to create [OVA](https://en.wikipedia.org/wiki/Open_Virtualization_Format) formatted images for RHEL and RHCOS, upload them to IBM Cloud Object storage and then import these images as boot images in your PowerVS service instance.

Further, the image disk should be minimum of 120 GB in size.

### Creating OVA images

- If you have PowerVC then you can follow the instructions provided in the [link](https://www.ibm.com/support/knowledgecenter/en/SSXK2N_1.4.4/com.ibm.powervc.standard.help.doc/powervc_export_image_hmc.html) to export an existing PowerVC image to OVA image.
- You can also use the following [python script](https://github.com/ocp-power-automation/infra/blob/master/scripts/images/convert_qcow2_ova.py) to convert Qcow2 image to OVA
  - RHEL 8.2 Qcow2 image is available from the following [link](https://access.redhat.com/downloads/content/279/ver=/rhel---8/8.2/ppc64le/product-software)
  - RHCOS Qcow2 image is available from the following [link](https://mirror.openshift.com/pub/openshift-v4/ppc64le/dependencies/rhcos/4.5/)

### Uploading to IBM Cloud Object Storage

- **Create IBM Cloud Object Storage service and bucket**
Please refer to the following [link](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage) for instructions to create IBM Cloud Object Storage service and required storage bucket to upload the OVA images.
<br>
- **Create secret and access keys with Hash-based Message Authentication Code (HMAC)**
Please refer to the following [link](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-uhc-hmac-credentials-main) for instructions to create the keys required for importing the images into your PowerVS service instance.
<br>
- **Upload the OVA image to Cloud Object storage bucket**
Please refer to the following [link](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-upload) for uploading the OVA image to the respective bucket. Alternatively you can also use the following [python script](https://github.com/ocp-power-automation/infra/blob/master/scripts/images/upload_image.py).


### Importing the images in PowerVS
Choose the previously created PowerVS "Service Instance", click "View full details" and select "Boot images".
Click the "Importing image" option and fill the requisite details like image name, storage type and cloud object storage details.

<details>
  <summary>Example screenshot showing import of RHEL image that is used for bastion</summary>

  ![Image Import-RHEL](./media/image-import1.png)
</details>

<details>
  <summary>Example screenshot showing import of RHCOS image used for OCP</summary>

  ![Image Import-RHCOS](./media/image-import2.png)
</details>

Your PowerVS service instance is now ready for OpenShift clusters.
