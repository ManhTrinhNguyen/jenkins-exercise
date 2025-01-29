

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



