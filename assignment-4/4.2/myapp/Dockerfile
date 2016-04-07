# This is a comment
FROM centos:centos6
MAINTAINER Joe the Developer <joe.dev@capgemini.com>

# Update the repository sources list and install some packages
RUN yum -y update; yum clean all
RUN yum -y install epel-release; yum clean all
RUN yum -y install nginx; yum clean all

# Create some files
RUN echo "daemon off;" >> /etc/nginx/nginx.conf
RUN echo "nginx on CentOS 6 inside Docker" > /usr/share/nginx/html/index.html

# Copy your project from the host to the image
COPY /www /usr/share/nginx/html

# Expose a port
EXPOSE 80

# By default, simply start nginx.
CMD [ "/usr/sbin/nginx" ]