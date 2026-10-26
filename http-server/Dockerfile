FROM node:12.16.3-stretch-slim AS frontend-build

WORKDIR /opt/front
COPY frontend/package.json frontend/yarn.lock frontend/babel.config.js ./
COPY frontend/src/ ./src/
COPY frontend/public/ ./public/
RUN npm install
RUN npm run build

FROM httpd:2.4

COPY --from=frontend-build /opt/front/dist/ /usr/local/apache2/htdocs/
COPY my-httpd.conf /usr/local/apache2/conf/httpd.conf
