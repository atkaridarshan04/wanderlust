#--- Stage-1 ---

FROM node:22 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm i

COPY . .

#--- Stage-2 ---

FROM node:22-alpine 

WORKDIR /app

COPY --from=builder /app .

COPY .env.sample .env

EXPOSE 5000

CMD ["npm", "start"]
