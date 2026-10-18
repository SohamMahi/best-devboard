FROM node:22.12-alpine AS build

WORKDIR /app

ENV NODE_ENV=development

COPY package.json package-lock.json ./

RUN npm ci --legacy-peer-deps --ignore-scripts

COPY . .

RUN npm run build


FROM node:22.12-alpine AS runtime

WORKDIR /app

ENV NODE_ENV=production

# Install only the tool required to serve the generated frontend.
RUN npm install --global vite@8.3.4 --ignore-scripts \
    && npm cache clean --force

COPY --from=build /app/dist ./dist
COPY vite.preview.config.js ./vite.config.js

USER node

EXPOSE 4173

ENTRYPOINT ["vite"]
CMD ["preview", "--host", "0.0.0.0", "--port", "4173"]
