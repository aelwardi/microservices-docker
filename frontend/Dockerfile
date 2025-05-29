FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM base AS build
COPY . .
RUN npm run build

FROM base AS development
WORKDIR /app
COPY . .
EXPOSE 5173 8080
CMD ["npm", "run", "dev"]

FROM node:20-alpine AS production
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/server.cjs ./server.cjs
COPY --from=build /app/package*.json ./
RUN npm ci --only=production
RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi && chown -R elwardi1:gelwardi /app
USER elwardi1
EXPOSE 8080
CMD ["node", "server.cjs"]