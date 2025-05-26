>wsl --list --online 
Install using 'wsl.exe --install <Distro>'
>wsl --install --distribution Ubuntu-24.04 
>wsl


$ sudo apt update 
$ sude apt install ansible -y
$ ssh-keygen


Step 2: Create Project Directory 
mkdir ansible-localhost 
cd ansible-localhost 
Step 3: Create an Inventory File

how to create invenoty file 
Using the Terminal 
1. Open your terminal. 

2. Navigate to your Ansible project folder (or create one): 
mkdir -p ansible-localhost 
cd ansible-localhost 

3.Run this command to create and open the file in a 
terminal-based editor: 
nano hosts.ini 

4. Paste this inside the file: 
[local] 
localhost ansible_connection=local 

$ mkdir ansible_localhost 
$ cd ansible_localhost 
$ nano hosts.ini 

5. Press CTRL + O CTRL + X

6. Use Ansible Modules
 ansible all -i hosts.ini -m ping 


localhost | SUCCESS => { 
  "ansible_facts": { 
     "discovered_interpreter_python": "/usr/bin/python3" 
  }, 
  "changed": false, 
  "ping": "pong" 
} 


7. Create and Understand a Playbook

$ nano 
ansible.yml 

- name: Install and run nginx on localhost
  hosts: local
  become: yes
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Start nginx
      service:
        name: nginx
        state: started
        enabled: yes


Press CTRL + O to save, then Enter, and CTRL + X to exit. 
Then you can visit: 
http://localhost 

$ cd ~/ansibleproject/ansible_localhost 
$ nano index.html 
$ ls ansible.yml  hosts.ini  index.html 


 1. Create a custom HTML file 
In your ansible_localhost folder, create a new file: 
index.html 
<!DOCTYPE html> 
<html> 
<head> 
<title>My Custom Nginx Page</title> 
</head> 
<body> 
<h1> Hello from Ansible!</h1> 
<p>This is a custom web page deployed using 
Ansible!</p> 
</body> 
</html> 
itishree@DESKTOP

$ nano ansible.yml 

 2. Update your ansible.yml playbook 
Add a new task that copies the index.html to the Nginx 
default location. 
Here’s your updated ansible.yml: --- 

- name: Install and run nginx on localhost
  hosts: localhost
  become: yes
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Copy custom index.html
      copy:
        src: index.html
        dest: /var/www/html/index.html
        owner: www-data
        group: www-data
        mode: '0644'

    - name: Start nginx service
      service:
        name: nginx
        state: started
        enabled: yes


Press CTRL + O to save, then Enter, and CTRL + X to exit. 
itishree@DESKTOP 
GVSEBGJ:~/ansibleproject/ansible_localhost$ ansible 
playbook -i hosts.ini ansible.yml 
�
� 4. Check your browser 
Go to http://localhost again 










o save, then Enter, and CTRL + X to exit. 
✅ Done! You’ve created your hosts.ini file.









========================================================================================================================

Great breakdown! You're essentially building a full CI/CD pipeline using:

* **Jenkins** for Continuous Integration
* **Maven** (v10 with JDK 17) for project builds
* **Ansible** for automated deployment

Here's a quick checklist and structure to ensure everything works smoothly:

---

### ✅ **Prerequisites**

* Jenkins installed and accessible
* Maven 10 + JDK 17 installed on Jenkins
* Ansible installed on Jenkins server or a connected node
* SSH access to deployment servers

---

### 📁 **Project Structure**

```
your-project/
├── Jenkinsfile           # Declarative pipeline script
├── deploy.yml            # Ansible playbook for deployment
├── inventory.ini         # Ansible inventory
└── target/my-app.jar     # Generated artifact after Maven build
```

---

### 📄 **Sample Jenkinsfile**

```groovy
pipeline {
  agent any
  tools {
    maven 'Maven-10' // Ensure this is configured under Jenkins Global Tools
    jdk 'JDK-17'
  }
  environment {
    DEPLOY_SERVER = "deploy@192.168.1.10"
  }
  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/your-user/your-repo.git'
      }
    }
    stage('Build') {
      steps {
        sh 'mvn clean install'
      }
    }
    stage('Archive Artifact') {
      steps {
        archiveArtifacts artifacts: 'target/*.jar'
      }
    }
    stage('Deploy with Ansible') {
      steps {
        ansiblePlaybook playbook: 'deploy.yml', inventory: 'inventory.ini'
      }
    }
  }
}
```

---

### 📄 **deploy.yml (Ansible Playbook)**

```yaml
---
- name: Deploy artifact to server
  hosts: web_servers
  become: yes
  tasks:
    - name: Copy the artifact to the remote server
      copy:
        src: target/my-app.jar
        dest: /opt/myapp/my-app.jar
        owner: root
        group: root
        mode: '0755'

    - name: Restart the application service
      systemd:
        name: myapp
        state: restarted
        enabled: yes

    - name: Check if the app is running
      uri:
        url: http://localhost:8080
        status_code: 200
```

---

### 📄 **inventory.ini**

```ini
[web_servers]
192.168.1.10 ansible_user=deploy
192.168.1.11 ansible_user=deploy
```

---

### 🟢 Final Tips:

* Make sure `myapp.service` exists on the remote server under `/etc/systemd/system/`.
* Use SSH keys for Jenkins → target server access.
* Add credentials in Jenkins securely if using private repos or SSH.
* Test Ansible separately before running via Jenkins for easier debugging.

q8bwd

Let me know if you want this packaged into a GitHub repo or need help writing the `myapp.service` file!
