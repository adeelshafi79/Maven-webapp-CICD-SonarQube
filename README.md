# Project Overview

In this project, we build and deploy a Java application. First, the application is built using Maven and tested with SonarQube. After passing the tests, the artifact is saved in the DockerHub registry. The entire CI process is automated using Jenkins. Following this, the updated manifests repository is integrated with ArgoCD, and the application is deployed to Kubernetes.

## Installation on an EC2 Instance


Install Jenkins, configure Docker as an agent, set up CI/CD, deploy applications to Kubernetes, and much more.

## AWS EC2 Instance

- Go to AWS Console
- Instances (running)
- Launch instances

![Launch Instances](https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png)

### Install Jenkins

**Pre-Requisites:**
- Java (JDK)

**Run the following commands to install Java and Jenkins:**

1. **Install Java:**

    ```bash
    sudo apt update
    sudo apt install openjdk-11-jre
    ```

2. **Verify Java Installation:**

    ```bash
    java -version
    ```

3. **Install Jenkins:**

    ```bash
    curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt-get update
    sudo apt-get install jenkins
    ```

**Note:** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as shown below.

- EC2 > Instances > Click on <Instance-ID>
- In the bottom tabs -> Click on Security
- Security groups
- Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed `All traffic`).

![Inbound Rules](https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png)

### Login to Jenkins using the below URL:

http://<ec2-instance-public-ip-address>:8080

[You can get the ec2-instance-public-ip-address from your AWS EC2 console page]

**Note:** If you do not want to allow `All Traffic` to your EC2 instance:
1. Delete the inbound traffic rule for your instance
2. Edit the inbound traffic rule to only allow custom TCP port `8080`

After you login to Jenkins:
- Run the command to copy the Jenkins Admin Password: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
- Enter the Administrator password

![Admin Password](https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png)

### Click on Install suggested plugins

![Install Plugins](https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png)

Wait for Jenkins to install the suggested plugins

![Plugin Installation](https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png)

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, it is better to create an admin user]

![Create Admin User](https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png)

Jenkins Installation is Successful. You can now start using Jenkins

![Jenkins Successful](https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png)

## Install the Docker Pipeline Plugin in Jenkins

- Log in to Jenkins.
- Go to Manage Jenkins > Manage Plugins.
- In the Available tab, search for "Docker Pipeline".
- Select the plugin and click the Install button.
- Restart Jenkins after the plugin is installed.

![Docker Pipeline Plugin](https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png)

Wait for Jenkins to restart.

## Docker Agent Configuration

Run the following commands to install Docker:

```bash
sudo apt update
sudo apt install docker.io
