# hello-jenkins

- Setup a GitHub repository:
  - Create GitHub repository and name it as "hello-jenkins".
  - Select the Node option from the Add .gitignore drop-down list.
  - Click the Create repository button, and our repo will be ready.
- Set up an AWS EC2 machine based on Ubuntu 14.04:
  - Go to the EC2 Management Console at https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#LaunchInstanceWizard:
  - Click "Launch Instance".
  - Choose "Ubuntu Server 14.04 LTS" and click "Select".
  - Select "t2.micro" and click "Next: Configure Instance Details".
  - Click "Next: Add storage".
  - Uncheck "Delete on Termination".
  - Click "Review and launch".
  - Click "Edit Security Groups".
  - Click "Custom" for the existing rule (SSH) and choose "Anywhere".
  - Click "Add Rule".
  - In "Port Range", write "8080" and in "Source", choose "Anywhere". In "Description" write "Jenkins".
  - Click "Add Rule".
  - In "Type" choose "HTTP". In "Source" choose "Anywhere". In "Description" write "HTTP".
  - Click "Review and Launch".
  - Click "Launch".
  - Click "Choose an existing key pair" if you already have one, or "Create a new key pair" if you haven't.
  - Check the "I acknowledge..." box.
  - Click "Launch Instances".
  - Click on "View Instances".
  - Wait until the instance is up and running.
  - Click on the pencil icon in your instance's cell uner the "Name" column and set a name for the instance: "TikalDemo".
- Connect to the new machine:
  - Go to the EC2 Management Console at https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#LaunchInstanceWizard:
  - Identify the new machine and copy its IP address.
  - Open an ssh-enabled terminal and use a command similar to the following to connect to your macihne:
    ssh -i 'C:\data\Private\SSH\.ssh\DVL2.pem' ubuntu@54.218.71.107
    Confirm with "yes".
- Install Git. Type:
  sudo apt install -y git
- Install node and npm. Type:
  sudo apt update
  sudo apt upgrade -y
  sudo apt install -y build-essential
  sudo apt install -y libssl-dev
  cd ~/git
  git clone https://github.com/nodejs/node.git
  cd ~/git/node
  git checkout v9.4.0
  ./configure
  make
  sudo make install
  node --version
  npm --version

- Clone the repository from GitHub to the machine:
  - mkdir ~/git
  - cd ~/git
  - git clone https://github.com/drorle/hello-jenkins
  
- Install dependencies:
  npm install --production
- Test by running the app:
  sudo node app.js
- To run the test, run:
  ./script/test

- Install forever to allow the app to run in the background:
  sudo npm install -g forever

- Allow node to run on port 80 without sudo:
  sudo setcap cap_net_bind_service=+ep /usr/local/bin/nodeo

- Run the app in the background:
  forever start app.js
- To stop the app, run:
  forever stopall

- Install Java 1.8:
  - Install java:
    sudo add-apt-repository ppa:openjdk-r/ppa
    sudo apt-get update
    sudo apt-get -y install openjdk-8-jdk

  - Choose java 8 as the default java:
    sudo update-alternatives --config java

- Install Jenkins:
  sudo wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
  sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
  sudo apt-get update
  sudo apt-get install -y jenkins

- Configure Jenkins
  - Get the default admin password from the file:
    /var/lib/jenkins/secrets/initialAdminPassword
  - Open a browser with the server ip address and port 8080
    e.g.: http://54.218.28.206:8080
  - Use the admin password to enter
  - Click on "Select plugins to install"
  - Search for and install "GitHub plugin"
  - Add relevant details in the "Create First Admin User" page and click "Save and Finish".
  - Click on "Start using Jenkins"
  - Click the New Item button. Use "hello-jenkins" for the item name, select Build a free-style software project, and click the button labeled OK.
  - Add the project's GitHub URL to the GitHub project box:
    https://github.com/drorle/hello-jenkins
  - Select the Git option under Source Code Management. In the newly appeared fields, add the URL to our GitHub project repo to the Repository URL field:
   https://github.com/drorle/hello-jenkins.git
  - Check the box next to "GitHub hook trigger".
  - Click the box to enable Build when a change is pushed to GitHub:
    Check box next to "Poll SCM".
    Fill "* * * * *" in the schedule (poll scm every minute).
  - Click the Add build step drop-down, and select Execute shell. In the command window type:
    npm install
    ./script/test
  - Click "Save".

- Configure GitHub integration with Jenkins
  - Head over to the GitHub repo, and click Settings. 
  - Click the Integrations & Services tab, and then on the Add service button.
  - Select the Jenkins (GitHub plugin) service.
  - Put the following address inside (you might need to change the IP address):
    http://54.218.28.206:8080/github-webhook/
  - Click Add service. 


- Test this new configuration by modifying some file in the GitHub project, commit and push it.


- Setup Deployment
  - Become user jenkins on the machine:
    sudo su jenkins
  - Generate an SSH key:
    ssh-keygen -t rsa
    Set the file to the default location and do not use a passphrase.
  - Copy the public key that was created. Run this:
    cat ~/.ssh/id_rsa.pub
    and copy the output.
  - Go back to the ubuntu user by running:
    exit
  - Create an authorized_keys file and paste the key the you copied:
    mkdir ~/.ssh
    vi ~/.ssh/authorized_keys
  - Set permissions on the ssh resources:
    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/*
  - Test this by setting into the jenkins user again and try to ssh into the ubuntu user:
    sudo su jenkins
    ssh ubuntu@54.218.28.206

