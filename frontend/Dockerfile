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

COPY .env.sample .env.local

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host"]
