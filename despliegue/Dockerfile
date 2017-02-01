FROM alpine
MAINTAINER Okynos <pylott@gmail.com>
WORKDIR /root
CMD ["/bin/ash"]

RUN apk update && apk upgrade && apk add nodejs 
RUN npm install express && npm install socket.io
