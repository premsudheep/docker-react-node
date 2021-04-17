# specify base Img (OS + Runtime env)
FROM node:14.16.1-alpine3.13

RUN addgroup app && adduser -S -G app app
USER app

WORKDIR /app
RUN mkdir data

# copy files to image under /app/ path under WORKDIR
COPY package*.json .

# run commands after copying source files
RUN npm install

COPY . .

# setting env variables
ENV API_URL=http://api.myapp.com/

EXPOSE 3000

# Shell form
# /bin/sh
# this requires to spin up another shell process
#CMD npm start

# pass --entrypoint arg to docker run cmd to ovverride npm start with another cmd
#ENTRYPOINT["npm", "start"]

# exec form
CMD ["npm", "start"]

