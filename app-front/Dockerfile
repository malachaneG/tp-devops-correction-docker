FROM node:12.16.3-stretch-slim AS build

WORKDIR /opt/front

COPY /app-front/src src/
COPY /app-front/public public/
COPY /app-front/package.json .
COPY /app-front/yarn.lock .
COPY /app-front/default.conf .
COPY /app-front/.env.production .

RUN npm install
RUN npm run build

FROM nginx:1.15.7-alpine

COPY --from=build /opt/front/dist /usr/share/nginx/html/
COPY --from=build /opt/front/default.conf /etc/nginx/conf.d/default.conf