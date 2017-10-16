# Image based on a ubuntu
FROM ubuntu

# File Author / Maintainer 
MAINTAINER Firstname Name <Firstname.Name@domain.tld>

# Update the repository sources list
RUN apt-get update

# Install apache2 php7  
RUN apt-get install -y apache2 php7.0 libapache2-mod-php7.0 php7.0-cli php7.0-common php7.0-mbstring php7.0-gd php7.0-intl php7.0-xml php7.0-mysql php7.0-mcrypt php7.0-zip  && apt-get clean

