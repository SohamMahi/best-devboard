# syntax=docker/dockerfile:1.7

FROM node:22-bookworm-slim AS build

WORKDIR /app

ENV NODE_ENV=development \
    NPM_CONFIG_AUDIT=false \
    NPM_CONFIG_FUND=false

COPY package.json package-lock.json ./

RUN --mount=type=cache,target=/root/.npm npm ci --legacy-peer-deps --ignore-scripts

COPY . .

RUN npm run build


FROM nginx:mainline-alpine AS runtime

RUN apk upgrade --no-cache

COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

USER nginx

EXPOSE 4173

CMD ["nginx", "-g", "daemon off;"]
