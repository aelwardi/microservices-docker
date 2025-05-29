FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./

FROM base AS development
ENV NODE_ENV=development
RUN npm install
COPY . .
EXPOSE 3002
CMD ["npm", "run", "dev"]

FROM base AS build
ENV NODE_ENV=production
RUN npm ci --omit=dev
COPY . .

RUN [ -f package.json ] && npm run build || echo "No build step"

FROM node:20-alpine AS production
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app/package*.json ./
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/src ./src
COPY --from=build /app/*.js ./
RUN addgroup -S gelwardi && adduser -S elwardi -G gelwardi
USER elwardi
EXPOSE 3002
CMD ["node", "src/app.js"]