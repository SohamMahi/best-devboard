FROM node:20-alpine AS build

WORKDIR /app

ENV NODE_ENV=development

COPY package.json package-lock.json ./

RUN npm ci --legacy-peer-deps --ignore-scripts

COPY . .

RUN npm run build

FROM node:20-alpine AS runtime

WORKDIR /app

ENV NODE_ENV=production

COPY package.json package-lock.json ./

# Vite is needed by the preview server.
RUN npm ci --legacy-peer-deps --ignore-scripts \
    && npm cache clean --force \
    && npm prune --omit=optional

COPY --from=build /app/dist ./dist
COPY vite.preview.config.js ./vite.config.js

USER node

EXPOSE 4173

CMD ["./node_modules/.bin/vite", "preview", "--host", "0.0.0.0", "--port", "4173"]
