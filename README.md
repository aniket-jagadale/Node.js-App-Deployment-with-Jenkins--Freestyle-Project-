
## 🚀 Node.js App Deployment with Jenkins (Freestyle Project)

This project outlines a **Continuous Integration/Continuous Deployment (CI/CD)** pipeline using **Jenkins Freestyle Projects** to automatically pull, build, and deploy a simple Node.js application.
![](img/Generated%20Image%20November%2001,%202025%20-%2012_39PM.png)
The pipeline is set up with four sequential jobs:

1.  **`01-setup-env`**: Installs essential system dependencies (Node.js, npm, PM2).
2.  **`02-node-pull`**: Pulls the latest application code from the Git repository.
3.  **`03-install-dependency`**: Installs the Node.js application's dependencies.
4.  **`04-node-deploy`**: Deploys (starts/restarts) the application using PM2.

Once the pipeline is successful, the Node.js application will be accessible at: `http://44.220.150.31:3000/`.

-----

### 🗺️ Deployment Architecture Diagram

This diagram shows the flow and dependencies between the four Jenkins jobs.

-----

### 🛠️ Prerequisites

  * A running **Jenkins** instance.
  * **Git** installed on the Jenkins server.
  * The Jenkins user must have the necessary permissions to execute system commands.
     * *Note: For this setup, initial dependencies are installed using `sudo` in `01-setup-env` to ensure tools are globally available, and then `npm install -g pm2` is run for the Jenkins user as per the PDF instructions to let Jenkins own the tools.* [cite: 8, 9]

-----

### ⚙️ Jenkins Job Configuration Details

Here are the key configurations for each Jenkins Freestyle job, including the **Execute Shell Commands** that make the deployment work.

#### 1\. `01-setup-env` (Setup Environment)

This is the initial setup job to ensure the necessary tools are installed on the server.

  * **Objective**: Install Node.js, npm, and PM2 globally.
  * **Build Steps** $\rightarrow$ **Execute shell**:

<!-- end list -->

```bash
sudo apt update
sudo apt install nodejs -y
sudo apt install npm -y
sudo npm install -g pm2
```

  * **Screenshot**:![](img/Screenshot%20(269).png)

-----

#### 2\. `02-node-pull` (Pull Repository)

This job pulls the application source code from the GitHub repository.

  * **Objective**: Fetch the latest code.
  * **Source Code Management**: **Git**
      * **Repository URL**: `https://github.com/iamtruptimane/node-js-app-CICD.git` [cite: 14]
      
      * **Branches to build**: `main` (or specified branch) [cite: 15]
  
  * **Screenshot**:![](img/Screenshot%20(270).png)

-----

#### 3\. `03-install-dependency` (Install Dependencies)

This job runs after the code is pulled and installs the required Node.js packages.

  * **Objective**: Run `npm install` in the workspace directory.
  * **Build Triggers**: **Build after other projects are built**
      * **Projects to watch**: `02-node-pull` (Ensures code is pulled first)
  * **Build Steps** $\rightarrow$ **Execute shell**:

<!-- end list -->

```bash
cd /var/lib/jenkins/workspace/02-node-pull/
sudo npm install
```

  * **Note**: The PDF suggests `cd "/var/lib/jenkins/workspace/node-pull-repo"` and `npm install`[cite: 24]. The screenshot uses a different directory name (`02-node-pull`) and includes `sudo`. We follow the steps from the screenshots/context provided.

  * **Screenshot**:
  ![](img/Screenshot%20(271).png)

-----

#### 4\. `04-node-deploy` (Deploy Application)

This final job starts or restarts the Node.js application using **PM2** (a process manager for Node.js).

  * **Objective**: Deploy the application.
  * **Build Triggers**: **Build after other projects are built**
      * **Projects to watch**: `03-install-dependency` (Ensures dependencies are installed)
  * **Build Steps** $\rightarrow$ **Execute shell**:

<!-- end list -->

```bash
cd /var/lib/jenkins/workspace/02-node-pull/
pm2 start app.js --name node-app || pm2 restart node-app
```

  * **Note**: This command first attempts to **start** the application with the name `node-app`. If it's already running (`start` fails), it executes a logical OR (`||`) and **restarts** the application. *The screenshot shows a slightly different command (`pm2 start app.js || pm2 restart app.js`), but the PDF suggests using an app name (`pm2 start app.js --name node-app || pm2 restart node-app`) [cite: 31] which is better practice. I have used the PDF's logical command structure for a more robust deployment. The screenshot uses the workspace of the pull job.

  * **Screenshot**:
![](img/Screenshot%20(272).png)
-----

### ✅ Deployment Status

  * The successful console output shows the application is running, managed by PM2.

  * The application is deployed and running in **fork mode**.

  * **Access the application**: `http://44.220.150.31:3000/`

      * *The port `3000` is the default port for this specific Node.js application.*

  * **Successful Deployment Console Output Snippet**:
  ![](img/Screenshot%20(276).png)