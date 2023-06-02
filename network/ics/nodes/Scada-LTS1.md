# Scada-LTS1 Configuration

The Scada-LTS1 is volatile, and we should reconfigure it whenever we reopen the project.

1. Start the node and then double click on it to open in your laptop's web browser. Login with default username/password: admin/admin

![image](../../../assets/Scada-LTS%20Login.png)

2. Go to `Scripting` from the toolbar, and then create a new script with name "Sample". Don't forget to click the save icon at the top right corner.

![image](../../../assets/Scada-LTS%20Script.png)

3. Next, in your laptop, open the terminal and SSH into your home desktop. Use `docker ps` to find the running Docker container "Scada-LTS1", and then `exec` into its bash shell.

![image](../../../assets/Scada-LTS%20Docker.png)

4. Enter the MySQL server in Scada-LTS1 container and use the "scadalts" database.

![image](../../../assets/Scada-LTS%20MySQL.png)

5. [Insert some records](./script-one-insert.txt) into the "scripts" table in "scadalts" database. After that, we can safely close the terminal.

![image](../../../assets/Scada-LTS%20Insert.png)

6. Go back to your web browser. After a refresh (F5), we will have many scripts.

![image](../../../assets/Scada-LTS%20Scripts.png)

7. Go to `Import/Export` from the toolbar, and then paste [the JSON](./Water%20Heater%203%20Stations.json) and click `Import`. You will see a lots of things be added.

![image](../../../assets/Scada-LTS%20Import.png)

8. Next, go to `Data sources` from the toolbar and you will see 3 sources. Delete the "Allen-Bradley" and "Modicon".

![image](../../../assets/Scada-LTS%20Source.png)

9. We begin to `edit` the Modbus data source. We change the host to `192.168.10.1` (OpenPLC1) and click the save icon at the top right corner. Then, we click `Scan for nodes` to find some nodes in OpenPLC1.

![image](../../../assets/Scada-LTS%20Modbus.png)

10.  After that, we go to `Graphical views` from the toolbar. Click "Edit view" icon, and then use `Choose File` and `Upload image` to upload [the background image](./background_combined.png). Click `Save` button after that.

![image](../../../assets/Scada-LTS%20Background.png)

11. Now, we can monitor and control OpenPLC1 from Scada-LTS1.
    - We can set the OpenPLC mode: "Auto/Manual". When in "Manual" mode, we can use "Heater On/Heater Off" to control the OpenPLC heater.
    - We can also adjust "Setpoint" value of OpenPLC.
    - Have fun!

![image](../../../assets/Scada-LTS%20OpenPLC.png)


