FROM node:14

# Create app directory
WORKDIR /usr/src/app
# /app can be

COPY package*.json ./

RUN npm install  
# default packagemanger for nodejs
# If you are building your code for production
# RUN npm ci --only=production

# copy all files into the image
COPY . .

EXPOSE 5000

CMD ["node","app.js"]  