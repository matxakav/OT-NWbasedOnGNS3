# Appliances

- [Appliances](#appliances)
	- [Import Appliances into GNS3 Server](#import-appliances-into-gns3-server)
	- [Configure Appliances](#configure-appliances)
	- [Create New Templates](#create-new-templates)
		- [New Versions of Existing Appliances](#new-versions-of-existing-appliances)
		- [New Appliances](#new-appliances)

Appliance templates are JSON files with the extension `.gns3a`.

Download additional appliance templates (apart from required templates in generic base network) as a [zip file](./ics-appliances.zip). You can also open them one by one in the current directory.

## Import Appliances into GNS3 Server

Refer to [this section](../../base/appliances/README.md#import-appliances-into-gns3-server).

## Configure Appliances

Some appliances such as Docker images require extra configuration due to the limitations of their templates. Right click on the appliance in the left sidebar and select `Configure template` to do so.

In practice, we should change the `HTTP port in the container` and the `HTTP path` in OpenPLC and Scada-LTS appliances.

- OpenPLC appliance uses HTTP port `8080`
- Scada-LTS appliance uses HTTP port `8080` and HTTP path `/Scada-LTS`

<div align=center>

<img src="https://user-images.githubusercontent.com/69375071/238611155-d5a0c510-676a-4475-9fce-b44570e60952.png" width="49%" />
<img src="https://user-images.githubusercontent.com/69375071/238611340-aaecd524-5b96-46fc-8189-0b690e1bc099.png" width="49%" />

</div>

<br/>

## Create New Templates

You may want to create new templates if you find some new images to use. These images can be new versions of existing appliances or new appliances.

### New Versions of Existing Appliances

Refer to [this section](../../base/appliances/README.md#new-versions-of-existing-appliances).

### New Appliances

Refer to [this section](../../base/appliances/README.md#new-appliances).
