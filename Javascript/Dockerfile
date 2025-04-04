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




