FROM nginx:alpine

RUN apk add --no-cache curl

COPY index.html /usr/share/nginx/html/index.html

ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]