#build stage
FROM node:12-stretch-slim AS build
WORKDIR /build
COPY package-lock.json package.json ./
RUN npm ci
COPY . .

# runtime stage
FROM alpine:3.10

#remove npm to make container even smaller
RUN apk add --update nodejs-current

RUN addgroup -S node && adduser -S node -G node

USER node

RUN mkdir /home/node/src

WORKDIR /home/node/src

COPY --from=build --chown=node:node /build .

CMD ["node","index.js"]