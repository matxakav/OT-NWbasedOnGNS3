# Appliances

- [Appliances](#appliances)
	- [Import Appliances into GNS3 Server](#import-appliances-into-gns3-server)
	- [Create New Templates](#create-new-templates)
		- [New Versions of Existing Appliances](#new-versions-of-existing-appliances)
		- [New Appliances](#new-appliances)

Appliance templates are JSON files with the extension `.gns3a`.

Download all required appliance templates as a [zip file](./base-appliances.zip). You can also open them one by one in the current directory.

- Concerning `.zip`, one should know that [it is also a TLD (Top-Level Domain)](https://www.youtube.com/watch?v=LFriS1PICE0) with security issues.

## Import Appliances into GNS3 Server

1. Select `Import appliance` from the menu bar of GNS3 client to open a `.gns3a` file. You can also double click on the `.gns3a` file to open it with GNS3 client.

![image](https://user-images.githubusercontent.com/69375071/210214639-95e7f455-faed-462c-9c72-3b93b19976d5.png)

2. Click `Next` twice for default options and then enter the `Install XXX appliance` window.

![image](https://user-images.githubusercontent.com/69375071/210214665-8bb7eddf-150a-4985-9130-d12d971f02d0.png)

3. Click on the subdirectory and notice that two buttons `Import` and `Download` appear at the bottom of the window. Click `Download` to open a download link for the image in your web browser.
   - Some appliances require multiple images. Make sure to download and import all of them.
   - Chinese users will need a VPN to download the images on Google Drive.

![image](https://user-images.githubusercontent.com/69375071/210214700-58abe174-740a-4b47-aa70-5b639d8cb842.png)

4. After you download the image, click `Import` to upload it to GNS3 server. On success, the appliance status will be `Ready to install`. Select the root directory `XXX verion X.X.X` and then click `Next` to complete the importing process.
   - `Next` button will not work if you do not select the root directory.

![image](https://user-images.githubusercontent.com/69375071/210248364-4164a55a-89d3-4f92-824b-28e33ceeb104.png)

5. Read the `Usage` carefully.

![image](https://user-images.githubusercontent.com/69375071/210248483-dfee17c9-ea86-4c1b-bee8-db25e9a9fc45.png)

6. You can review the `Usage` by right clicking the appliance from the left sidebar, selecting `Configure template`, and navigating to `Usage` section.

<div align=center>

<img src="https://user-images.githubusercontent.com/69375071/210248750-321402a7-896c-481b-97f3-739dff6248f9.png" width="49%" />
<img src="https://user-images.githubusercontent.com/69375071/210248600-baa8416d-2215-4327-830c-b4ddf691f657.png" width="49%" />

</div>

<br/>

## Create New Templates

You may want to create new templates if you find some new images to use. These images can be new versions of existing appliances or new appliances.

### New Versions of Existing Appliances

Refer to existing templates in the current directory. Take [opx-openswitch.gns3a](./opx-openswitch.gns3a) as an example.

You should add a new image and a new version. Say the image is `NewImage.qcow2` with version `1.0.0`, size `123456789` (Byte), and md5sum `1234567890987654321abcdefghijklm`.

- In any Linux distro, you can use `ls -l` to get the image size in Byte, and `md5sum NewImage.qcow2` to get the md5sum.

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
            "md5sum": "1234567890987654321abcdefghijklm",
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

Search on the [GNS3 marketplace](https://gns3.com/marketplace/appliances/) for templates first. In most cases, you would find the right one to use.

If the template in GNS3 marketplace doesn't contain your image version, then you can add it following the above instruction.

If you can't find the template in GNS3 marketplace, then you can create your own template by modifying any existing ones.
