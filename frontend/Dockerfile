FROM node:21-alpine 

WORKDIR /app

COPY package.json /app

RUN npm install -g pnpm

RUN pnpm install

COPY . /app

RUN pnpm build 

EXPOSE 4173

CMD ["pnpm", "preview"]