# Lab Setup 

# Clone the lab repo in VS Code

### Step 1: Clone the repo

1. Open Visual Studio Code
2. In Visual Studio Code, click **Clone Repository** and paste `https://github.com/jruels/docker-fun`
3. Hit **Enter**, and in the pop-up window, browse to `C:\Users\tekstudent\Downloads\repos`
4. Click **Select as repository destination**
5. When prompted to open the cloned repo, choose **Open**.
6. After opening the folder, click the third icon in the left toolbar for source control. Next to **changes**, click the ellipses (three dots) and choose **pull**.

## Set up a remote SSH session in Visual Studio Code.   

### Create the SSH configuration file.

On the left sidebar, click the icon that looks like a computer with a connection icon.

In the Remote Explorer, hover your mouse cursor over **SSH**, click on the gear icon (⚙️) in the top right corner, and select the top option: `C:\Users\tekstudent\.ssh\config` This will open the SSH configuration file in a new editor tab.


### Add the SSH configuration for the lab servers.

Add the following to the SSH configuration file, replacing `<IP of Server>` with the actual IP address.

```plaintext
Host leader
  HostName <IP of Server>
  IdentityFile "C:\Users\tekstudent\Downloads\repos\docker-fun\keys\lab.pem"
  User ubuntu
```

### Save the SSH configuration file.

Save the changes to the SSH configuration file and close it.


### Connect to the lab server.

1. In the Remote Explorer, you should now see the entry for the server under "SSH Targets."
2. Click on the entry to connect.
3. Visual Studio Code will open a new window connected to the server.
4. When prompted for the Operating System, choose 'Linux'.  
5. Accept the SSH fingerprint
6. You can now open a terminal in this new window and run commands on the remote server.

In the VS Code window connected to the server, open a terminal, and run: 

```bash
git clone https://github.com/jruels/docker-fun
```



### Create a working directory.

In Visual Studio Code, you can create a new folder or file as if it were on your local machine.
Click **Open Folder** and select `/home/ubuntu`.
In future labs, you will create a directory for each lab.


