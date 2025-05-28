FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build


FROM build

WORKDIR /app

COPY --from=build /app/dist ./dist
COPY --from=build /app/server.cjs ./server.cjs
COPY package*.json ./
RUN npm install --production

EXPOSE 8080

CMD ["node", "server.cjs"]