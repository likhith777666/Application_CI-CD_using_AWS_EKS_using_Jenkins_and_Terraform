FROM node:14
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm cache clean --force \
 && npm install --no-audit --no-fund
 
COPY . .
CMD ["node", "index.js"]
