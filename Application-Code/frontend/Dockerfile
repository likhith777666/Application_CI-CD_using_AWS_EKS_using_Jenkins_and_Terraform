FROM node:18

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm config set registry https://registry.npmjs.org/ \
 && npm config set fetch-retries 5 \
 && npm config set fetch-retry-mintimeout 20000 \
 && npm config set fetch-retry-maxtimeout 120000 \
 && npm install --no-audit --no-fund

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
