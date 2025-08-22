Jenkins Task
Creating 03 VMs
Configurations
JenkinsWorking
	NAT_01:10.10.1.100
	HostOnly #2:100.0.0.100
	HostName: jenkins
	Users:
		iqbal -> root wheel
		Jenkins -> root wheel
	Sudoer File:
		jenkins ALL=(ALL)       ALL
		%wheel  ALL=(ALL)       NOPASSWD: ALL
		
DevWorking
	NAT_01:10.10.1.101
	HostOnly #2:100.0.0.101
	HostName: DevWorking
	Users:
		iqbal -> root wheel
		Jenkins -> root wheel
	Sudoer File:
		jenkins ALL=(ALL)       ALL
		%wheel  ALL=(ALL)       NOPASSWD: ALL
		
WebServer
	NAT_01:10.10.1.102
	HostOnly #2:100.0.0.102
	HostName: webserver
	Users:
		iqbal -> root wheel
		Jenkins -> root wheel
	Sudoer File:
		jenkins ALL=(ALL)       ALL
		%wheel  ALL=(ALL)       NOPASSWD: ALL
		
ssh-copy-id jenkins@<all-VMs>

Reboot all VMs
------------------------------------------------------
Installations
JenkinsVM
Installing Jenkins on JenkinsWorking (Ref: https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos)
	sudo wget -O /etc/yum.repos.d/jenkins.repo \
   		 https://pkg.jenkins.io/redhat-stable/jenkins.repo
	sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
	sudo dnf upgrade
Install required dependencies for the jenkins package
	sudo dnf install fontconfig java-21-openjdk
	sudo dnf install jenkins
	sudo systemctl daemon-reload
	sudo systemctl enable Jenkins
	sudo systemctl start Jenkins
	sudo systemctl status jenkins
Adding exception in the Firewall (firewall-cmd)
	sudo firewall-cmd --permanent --add-port=8080/tcp
	sudo firewall-cmd --reload
Port forwarding may be needed if running in VirtualBox
Open browser type:
	localhost:8080 (As per your installation and environment)
obtain admin Password from:
	cat /var/lib/jenkins/secrets/initialAdminPassword
Install RSYNC:	
	 sudo dnf install rsync -y
Install GIT:
 	user: Jenkins
 	Command: sudo dnf install git -y
-----------------------------------------------------
WorkingVM
Installing GIT on DevWorking
	user: Jenkins
	Command: sudo dnf install git -y
	Make a Directory: mkdir <website>
			  cd <website>
Setting SSH at GitHub
	ssh-keygen -t rsa 
	cat .ssh/id_rsa.pub #Public key of the working user
	copy all content of id_rsa.pub file
	goto GITHUB
	Login and goto Settings
	Goto SSH and GPG Keys
	Give Name to the key and paste all contents of id_rsa.pub file
	Save and continue
	type:
	ssh -T git@github.com #to create ssh connection between your machine/VM and github
	Save and continue
Create New Repo on GitHub
	select name of you repo
	click Create, goto your working directory in your machine/VM and follow these steps:
	echo "# website" >> README.md
	git init
	git add README.md
  	git config --global user.email "<you@example.com>"
  	git config --global user.name "<Your Name>"
	git commit -m "first commit"
	git branch -M main
	git remote add origin <git@github.com:iqbalsound/website.git>
	git push -u origin main
-----------------------------------------------------
WebServerVM
Installing Jenkins on Webserver
	sudo dnf install nginx -y
	sudo systemctl status nginx
	sudo systemctl enable nginx
	sudo systemctl start nginx
	Adding Service to Firewall
	sudo firewall-cmd --permanent --add-service=http --zone=public
	sudo firewall-cmd --reload
Port forwarding may be needed if running in VirtualBox
Install RSYNC:
 	 sudo dnf install rsync -y
Setting access to /usr/share/nginx/html/
	sudo chmod jenkins:root /usr/share/nginx/html
	sudo usermod -a -G root jenkins
------------------------------------------------------

JenkinsWeb01
Steps to be done initially on <Jenkins_VM-ON-AZURE>
 	1  sudo passwd root
	2  sudo dnf update -y && sudo dnf install nano wget git -y
	3  sudo dnf install firewalld -y
	4  sudo usermod -a -G root jenkins
	5  sudo usermod -a -G wheel Jenkins
	6  sudo dnf install nginx -y
	7  sudo dnf install firewalld -y

Adding exception in the Firewall (firewall-cmd)
 	sudo firewall-cmd --permanent --add-service=http
 	sudo firewall-cmd --reload

Adding Jenkins user in sudoers file
	sudo nano /etc/sudoers
	add under 	root 	ALL=(ALL)	ALL
			Jenkins	ALL=(ALL)	ALL
	Add # before 	%wheel ALL=(ALL)        ALL
	remove # before %wheel  ALL=(ALL)       NOPASSWD: ALL

Adding ports in inbound rules
	JenkinsWeb01 -> Network Settings -> Network security group click Create port rule
	Set as following:
	Source 				Any
	source port ranges*		 *
	Destination			Any
	Service				Custom
	Destination port ranges		8080
	Protocol			Any
	Action				Allow
	Priority			1010

Installing Jenkins on JenkinsWeb01 (Ref: https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos)
 	sudo wget -O /etc/yum.repos.d/jenkins.repo \
   		 https://pkg.jenkins.io/redhat-stable/jenkins.repo
 	sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
 	sudo dnf upgrade
Install required dependencies for the jenkins package
 	sudo dnf install fontconfig java-21-openjdk
 	sudo dnf install jenkins
 	sudo systemctl daemon-reload
 	sudo systemctl enable Jenkins
 	sudo systemctl start Jenkins
 	sudo systemctl status jenkins
Adding exception in the Firewall (firewall-cmd)
 	sudo firewall-cmd --permanent --add-port=8080/tcp
 	sudo firewall-cmd --reload

Open browser type:
 	<publicIP>:8080
obtain admin Password from:
 	sudo cat /var/lib/jenkins/secrets/initialAdminPassword


Sample access to /usr/share/nginx/html
	drwxrwxr-x. 5 jenkins nginx 4096 Aug 21 12:57 /usr/share/nginx/html























