# OpenPLC1 Configuration

The OpenPLC1 is volatile, and we should reconfigure it whenever we reopen the project.

1. Start the node and then double click on it to open in your laptop's web browser. Login with default username/password: openplc/openplc

![image](../../../assets/OpenPLC%20Login.png)

1. After you enter the OpenPLC's dashboard, go to `Settings` from the left sidebar and disable the DNP3 server and Ethernet/IP server, and then `Save Changes`.

![image](../../../assets/OpenPLC%20Server.png)

3. Next, go to `Programs` and then use `Choose File` and `Upload Program` to upload a sample program: [water_heater.st](./water_heater.st).

![image](../../../assets/OpenPLC%20Programs.png)

4. Give the program a name and click `Upload program`. After compiling, click `Go to Dashboard`.

![image](../../../assets/OpenPLC%20Program%20Name.png)

5. Now, we can `Start PLC` from the left sidebar and note that the OpenPLC1 is in "Running" status.

![image](../../../assets/OpenPLC%20Running.png)

6. Go to `Monitoring` and we can see the dummy output from the water_heater.st program.

![image](../../../assets/OpenPLC%20Monitoring.png)
