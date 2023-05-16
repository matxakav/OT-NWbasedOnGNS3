# Appliances

- [Appliances](#appliances)
	- [Import Appliances into GNS3 Server](#import-appliances-into-gns3-server)
	- [Configure Appliances](#configure-appliances)
	- [Create New Templates](#create-new-templates)
		- [New Versions in Existing Appliances](#new-versions-in-existing-appliances)
		- [New Appliances](#new-appliances)

Download all required appliance templates as a [zip file](./appliances.zip).

## Import Appliances into GNS3 Server

1. Select `Import appliance` from the menu bar of GNS3 client to open a `.gns3a` file.

![image](https://user-images.githubusercontent.com/69375071/210214639-95e7f455-faed-462c-9c72-3b93b19976d5.png)

2. Click `Next` twice for default options and then enter the `Install XXX appliance` window.

![image](https://user-images.githubusercontent.com/69375071/210214665-8bb7eddf-150a-4985-9130-d12d971f02d0.png)

3. Click on the subdirectory and notice that two buttons `Import` and `Download` appear at the bottom of the window. Click `Download` to open a download link for the image in your web browser.
   - Some appliances require multiple images. Make sure to download and import all of them.

![image](https://user-images.githubusercontent.com/69375071/210214700-58abe174-740a-4b47-aa70-5b639d8cb842.png)

4. After you download the image, click `Import` to upload it to GNS3 server. On success, the appliance status will be `Ready to install`. Select the root directory `XXX verion X.X.X` and then click `Next` to complete the importing process.
   - `Next` button will not work if you do not select the root directory.

![image](https://user-images.githubusercontent.com/69375071/210248364-4164a55a-89d3-4f92-824b-28e33ceeb104.png)

5. Read the `Usage` carefully before configuring the network.

![image](https://user-images.githubusercontent.com/69375071/210248483-dfee17c9-ea86-4c1b-bee8-db25e9a9fc45.png)

6. You can review the `Usage` by right clicking the appliance from the sidebar, selecting `Configure template`, and navigating to `Usage` section.

<div align=center>

<img src="https://user-images.githubusercontent.com/69375071/210248750-321402a7-896c-481b-97f3-739dff6248f9.png" width="49%" />
<img src="https://user-images.githubusercontent.com/69375071/210248600-baa8416d-2215-4327-830c-b4ddf691f657.png" width="49%" />

</div>

## Configure Appliances

Some appliances such as Docker images require extra configuration due to the limitations of their templates.

In practice, we should change the `HTTP port` and `HTTP path` in OpenPLC and Scada-LTS.

<div align=center>

<img src="https://user-images.githubusercontent.com/69375071/238611155-d5a0c510-676a-4475-9fce-b44570e60952.png" width="49%" />
<img src="https://user-images.githubusercontent.com/69375071/238611340-aaecd524-5b96-46fc-8189-0b690e1bc099.png" width="49%" />

</div>

## Create New Templates

You may want to create new templates if you find some new appliances to use.

### New Versions in Existing Appliances

Refer to existing templates in the current directory. Take [opx-openswitch.gns3a](./opx-openswitch.gns3a) as an example.

You should add a new image and a new version. Say the image is NewImage.qcow2 with version 1.0.0. The image size is 123456789 Byte and its md5sum is XXX.

```json
{
	"images": [
        {
            "filename": "OpenSwitch.qcow2",
            "version": "0.4.0",
            "md5sum": "1989d5653718e0de830dec68a0475ac1",
            "filesize": 457572352,
            "download_url": "https://drive.google.com/drive/folders/1PfXBkzPgJDw7eFsGfKvLYbGgeZO9YKKy"
        },
		{
            "filename": "NewImage.qcow2",
            "version": "1.0.0",
            "md5sum": "XXX",
            "filesize": 123456789,
            "download_url": "https://some_download_url"
        }
    ],
    "versions": [
        {
            "name": "0.4.0",
            "images": {
                "hda_disk_image": "OpenSwitch.qcow2"
            }
        },
		{
            "name": "1.0.0",
            "images": {
                "hda_disk_image": "NewImage.qcow2"
            }
        }
    ]
}
```

### New Appliances

You may find GNS3 marketplace for templates.
