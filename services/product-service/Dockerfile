FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./

FROM base AS development
ENV NODE_ENV=development
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "run", "dev"]

FROM base AS build
ENV NODE_ENV=production
RUN npm ci --omit=dev
COPY . .
RUN npm run build || true

FROM node:20-alpine AS production
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app ./
RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi
USER elwardi1
EXPOSE 3000
CMD ["node", "src/app.js"]