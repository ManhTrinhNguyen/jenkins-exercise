

# "Build Automation with Jenkins"

##### Test
The project uses jest library for tests. (see "test" script in package.json)
There is 1 test (server.test.js) in the project that checks whether the main index.html file exists in the project. 

To run the nodejs test:

    npm run test

Make sure to download jest library before running test, otherwise jest command defined in package.json won't be found.

    npm install

In order to see failing test, remove index.html or rename it and run tests.

## Step 1 : Dockerize Node App 
    ```
        ## To run Node App i need Nodejs as a Base image
        FROM node:20-alpine 
        
        ## RUN (Execute any Linux cmd inside the container) . 
        ## I Create a /home/app dir inside the container
        RUN mkdir -p /home/app 
        
        ## COPY (To Copy from the App in the Host to Destination in the container)
        COPY ./app /home/app 
        
        ## WORKDIR : Make sure the container working on this Dir
        WORKDIR /home/app 
        
        ## Install npm 
        RUN npm install 
        
        ## CMD : Help me execute the entrypoint
        CMD [ "node", "server.js" ]

    ```

## Step 2 : Create a full pipeline for NodeJS App
1. Rent a EC2 sercer on AWS to run Jenkins as a Docker container 
    - **Step to create EC2 Instance :**
        ```
            1. Add Tags
            2. Choose OS Image
            3. Choose Capacity
            4. Network Configuration
            5. Configure Security Group
            6. Add storage
        ```

    - **Connect to Intance :**
        ```
            1. Move .pem (key pair) to more secure location ~./ssh/
            2. Change persion of .pem to make permission stricter with only me as a user can read that (chmod 400 instancefile)
            3. Connect to instance : ssh -i ~/.ssh/docker-server.pem ec2-user@Ipv4
                    -i: This flag will take the pem file as a parameter
                    ec2-user: I need to ssh to instance as a ec2 user not as a root user
        ```

    - **Install Docker :**
    ```
    - Update Package Manager: `sudo yum update`
    - Install Docker: `sudo yum install docker` 
    - When docker installed I have to start docker Daemon : sudo service docker start
    - I want to run docker command without using sudo . I will add user to Docker group: sudo usermod -aG docker $USER . After running  this command user may not add to docker group yet . I need to exit and login again
    ```

    - **Install Jenkins**
    ```
     - Run Jenkins as a Docker Container: `docker run -d -p 8080:8080 -p 50000:50000 -v jenkins-home:/var/jenkins-home -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins`
    
        -d: Detach mode
        -p 8080:8080 : Jenkins run on port 8080
        -v jenkins-home:/var/jenkins-home: Create jenkins volumn to persist data
        -v /var/run/docker.sock:/var/run/docker.sock: This will mount docker CLI to Jenkins container so docker CLI will available in jenkins container
        jenkins/jenkins: Jenkins Image

    - Install Docker Inside Jenkins container so I have Docker CLI available in Jenkins : `curl https://get.docker.com/ > dockerinstall && chmod 777 dockerinstall && ./dockerinstall`
    - Inside jenkins container as root: `chmod o=rw /var/run/docker.sock` to make other user can use docker CLI in the container
    ```
    
2. When Jenkins available I can take the IP Address and the port to access. 
    ```
    - To create a Node Pipeline I need to configure Credentials to get access to source code 
    - Configure Nodejs so that Node will be available in Jenkins 
    - Configure Webhook Trigger pipeline to trigger pipeline whenever developer push code to a Repo
    - Create Jenkinsfile to create pipeline 
        - From Pipeline enable : GitHub hook trigger for GITScm polling
        - From Github : `In the Repo go to : Setting -> Webhook -> Add Webhook`
    ```

    - **Dynamic increment App version in Nodejs**
    ```
    1. npm install -D auto-version-js
    2. 
     - npx auto-version --patch # +0.0.1
     - npx auto-version --minor # +0.1.0
     - npx auto-version --major # +1.0.0
     - npx auto-version # no args is equivalent to --patch
    3. Install Plugin to use "readJSON file" : Pipeline: Utility Steps 
    ```

    - **Auto Commit to Git and**
    ```
    NOTE: Make sure to user withCredentials to get USER and PASS
    1. Connect to Git Repo Source : 
        - git remote set-url origin https://${USER}:${PASS}@github.com/ManhTrinhNguyen/jenkins-exercise.git
    2. Configure user name and email to set Stop the Webhook (When it auto commit and push Jenkins will not re run the pipeline job)
        - git config --global user.email jenkins@gmail.com
        - git config --global user.name Jenkins
    3. Add, Commit and Push
        - git add .
        - git commit -m "ci: bump version"
        - git push origin HEAD:main

    4 . Stop Web Hook to not re-run by using username :
        - In the Pipeline jobs -> Configure -> Additional Behavior -> Polling ignores commits from certain users
    ```

## Step 3 : : Extract into Jenkins Shared Library 

    1. **Create My shared lib Repo** (https://github.com/ManhTrinhNguyen/Jenkins-Docker-Excercise-Shared-Library)
    2. Get My Share Lib :
    ```
        library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
            [$class: 'GitSCMSource',
             remote: https://github.com/ManhTrinhNguyen/Jenkins-Docker-Excercise-Shared-Library.git,
             credentialsId: 'github-credentials'
            ]
        )

    3. Or I want to put my Shared Lib in Global : 
        - From Jenkins Manage -> Systems -> Global Trusted Pipeline Libraries -> Configure name, github url, credentials 
        - In Jenkinsfile : @Library('name of my library shared')
    ```