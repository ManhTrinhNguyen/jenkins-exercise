

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
1. Rent a Droplet on Digital Ocean to run Jenkins as a Docker container 
    - In the Server
        - Update Package Manager: `apt update`
        - Install Docker: `apt install docker.io` 
        - Run Jenkins as a Docker Container: `docker run -d -p 8080:8080 -p 50000:50000 -v jenkins-home:/var/jenkins-home -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins`
            ```
                -d: Detach mode
                -p 8080:8080 : Jenkins run on port 8080
                -v jenkins-home:/var/jenkins-home: Create jenkins volumn to persist data
                -v /var/run/docker.sock:/var/run/docker.sock: This will mount docker CLI to Jenkins container so docker CLI will available in jenkins container
                jenkins/jenkins: Jenkins Image
            ```

