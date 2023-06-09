FROM node:16.15.0 as build-stage
WORKDIR /var/jenkins_home/workspace/deploy/front
COPY ./shopmobile/package.json .
COPY ./shopmobile/package-lock.json .
RUN npm install
COPY ./shopmobile .
RUN npm run build

FROM node:16.15.0 as kiosk
WORKDIR /var/jenkins_home/workspace/deploy/kiosk
COPY ./kiosk/package.json .
COPY ./kiosk/package-lock.json .
RUN npm install
COPY ./kiosk .
RUN npm run build

FROM node:16.15.0 as admin
WORKDIR /var/jenkins_home/workspace/deploy/admin
COPY ./adminpage/package.json .
COPY ./adminpage/package-lock.json .
RUN npm install
COPY ./adminpage .
RUN npm run build


FROM nginx:stable-alpine as production-stage

COPY --from=build-stage /var/jenkins_home/workspace/deploy/front/build /usr/share/nginx/html/app
COPY --from=kiosk /var/jenkins_home/workspace/deploy/kiosk/build /usr/share/nginx/html/kiosk
COPY --from=admin /var/jenkins_home/workspace/deploy/admin/build /usr/share/nginx/html/admin
COPY --from=build-stage /var/jenkins_home/workspace/deploy/front/deploy_conf/nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
EXPOSE 443
CMD ["nginx", "-g","daemon off;"]