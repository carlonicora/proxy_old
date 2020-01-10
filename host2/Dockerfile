FROM nginx:1.17-alpine

COPY ./host2/configurations/nginx.conf /etc/nginx/
COPY ./host2/hosts/* /etc/nginx/sites-available/

WORKDIR /etc/nginx/

CMD ["nginx"]

EXPOSE 80