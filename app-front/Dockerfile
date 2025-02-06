FROM node:12.16.3-stretch-slim AS build

WORKDIR /opt/front

COPY /devops-front-main/src src/
COPY /devops-front-main/public public/
COPY /devops-front-main/package.json .
COPY /devops-front-main/yarn.lock .
COPY /devops-front-main/default.conf .
COPY /devops-front-main/.env.production .

RUN npm install
RUN npm run build

FROM nginx:1.15.7-alpine

COPY --from=build /opt/front/dist /usr/share/nginx/html/
COPY --from=build /opt/front/default.conf /etc/nginx/conf.d/default.conf