FROM nginx:1.17-alpine

COPY ./host1/configurations/nginx.conf /etc/nginx/
COPY ./host1/hosts/* /etc/nginx/sites-available/

WORKDIR /etc/nginx/

CMD ["nginx"]

EXPOSE 80