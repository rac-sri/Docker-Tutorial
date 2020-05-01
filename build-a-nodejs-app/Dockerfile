FROM node:12-stretch-slim

USER node

WORKDIR /home/node/src

COPY  --chown=node:node index.js index.js

ADD  --chown=node:node index.js index.js

CMD [ "node" , "index.js" ]