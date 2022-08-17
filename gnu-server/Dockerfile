FROM centos
ENV TERM=xterm-256color

#Add Dependencies
RUN sed -i -e "s|mirrorlist=|#mirrorlist=|g" /etc/yum.repos.d/CentOS-*
RUN sed -i -e "s|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g" /etc/yum.repos.d/CentOS-*
RUN yum update -y
RUN yum -y install git wget patch python3 python3-tools python3-pip python3-ldap 2to3 which tar sudo 
##Get and Install nodejs
# RUN set -o pipefail && wget -qO- https://rpm.nodesource.com/setup_13.x | bash -
# RUN yum -y install nodejs
# RUN npm install -g grunt-cli
# RUN pip3 install ldap3

#Tidy up
RUN yum -y clean all && rm -rf /var/yum/cache

#RUN npm install bower install grunt

#Add GNUHealth User
RUN adduser -m -d /home/gnuhealth gnuhealth && \
    echo "gnuhealth ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/gnuhealth && \
    chmod 0440 /etc/sudoers.d/gnuhealth

#Make Python3 Default python binary
#RUN rm /usr/bin/python && ln -si /usr/bin/python3 /usr/bin/python
RUN ln -s /usr/local/bin/python3 /usr/bin/python

# Prueba
RUN pip3 install setuptools
RUN pip3 install --upgrade setuptools
RUN pip3 install --upgrade pip

#Switch to GNUHealth User
USER gnuhealth
ENV HOME /home/gnuhealth
WORKDIR /home/gnuhealth

#Get GNUHealth
RUN mkdir $HOME/gnuhealth-latest
# RUN set -o pipefail && wget -qO- https://ftp.gnu.org/gnu/health/gnuhealth-latest.tar.gz | tar xzf - --directory gnuhealth-latest --strip-components=1
COPY gnuhealth-4.0.3/ $HOME/gnuhealth-latest
WORKDIR $HOME/gnuhealth-latest

RUN sudo chown -R gnuhealth $HOME/gnuhealth-latest
#RUN set -o pipefail && wget -qO- https://ftp.gnu.org/gnu/health/gnuhealth-setup-latest.tar.gz | tar -xzvf -

#Fix werkzeug version to prevent "No module named 'werkzeug.contrib'"
#RUN sed -i -E "s/werkzeug/\werkzeug==0.16.1/" gnuhealth-setup

#Install GNUHealth
COPY gnuhealth-setup $HOME/gnuhealth-latest/gnuhealth-setup
RUN $HOME/gnuhealth-latest/gnuhealth-setup install

WORKDIR $HOME

# # Listen on 0.0.0.0 to expose outside container
RUN sed -E -i "s/^listen = \*:8000/listen = 0.0.0.0:8000/g" $HOME/gnuhealth/tryton/server/config/trytond.conf
RUN sed -E -i "s/^listen = \*:8080/listen = 0.0.0.0:8080/g" $HOME/gnuhealth/tryton/server/config/trytond.conf

# # Output logs
# #RUN ln -sf /dev/stdout /home/gnuhealth/gnuhealth/logs/gnuhealth.log

# EXPOSE 8000
#Copy custom gnuhealthrc which contains Docker ENV Variables.
COPY gnuhealthrc $HOME/.gnuhealthrc
#COPY gnuhealth_log.conf $HOME/gnuhealth/tryton/server/config/gnuhealth_log.conf
ENTRYPOINT ["/home/gnuhealth/start_gnuhealth.sh"]



# FROM debian:10
# #FROM tryton/tryton:6.2
# CMD ["/bin/bash"]
# USER root
# #ENV T_MAJOR 6.0

# RUN apt-get update && apt-get install -y --no-install-recommends \
#     gzip curl ca-certificates vim python3 python3-pip lsb-release nginx

# #RUN apt-get install nodejs npm build-essential -y \
# #    && curl -sL https://deb.nodesource.com/setup_12.x | bash - 
# #RUN npm install -g grunt-cli

# RUN apt-get install patch python3-ldap 2to3 tar sudo wget gnupg bash unoconv libarchive-tools git systemd -y 

# RUN du -ch /var/cache/apt/archives/

# RUN useradd -m -d /home/gnuhealth gnuhealth && \
#     echo "gnuhealth ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/gnuhealth && \
#     chmod 0440 /etc/sudoers.d/gnuhealth
# #RUN rm /usr/bin/python && ln -si /usr/bin/python3 /usr/bin/python
# USER gnuhealth
# ENV HOME /home/gnuhealth
# WORKDIR /home/gnuhealth
# #RUN mkdir $HOME/gnuhealth-latest

# #ENV PATH=/home/gnuhealth/.local/bin
# RUN python3 -m pip install -U pip 
# RUN python3 -m pip install -U setuptools
# RUN pip3 install ldap3 \
#     trytond==6.0 flask==1.0 gunicorn==19.9

# ## Actualizar pip
# # RUN pip3 install --upgrade pip

# ## Copiar directorio gnuhealth
# RUN mkdir $HOME/gnuhealth-latest
# COPY gnuhealth-4.0.3/ $HOME/gnuhealth-latest

# WORKDIR $HOME/gnuhealth-latest
# RUN sudo chown -R gnuhealth $HOME/gnuhealth-latest
# RUN pip3 install --upgrade pip \
#     && pip3 install trytond==6.0

# ## Copiar instalador de gnuhealth
# COPY gnuhealth-setup $HOME/gnuhealth-latest/gnuhealth-setup
# RUN sudo chmod 775 gnuhealth-setup
# RUN $HOME/gnuhealth-latest/gnuhealth-setup install

# RUN sed -E -i "s/^listen = \*:8000/listen = 0.0.0.0:8000/g" $HOME/gnuhealth/tryton/server/config/trytond.conf
# RUN sed -E -i "s/^listen = \*:8080/listen = 0.0.0.0:8080/g" $HOME/gnuhealth/tryton/server/config/trytond.conf

# COPY start_gnuhealth.sh /home/gnuhealth/start_gnuhealth.sh
# RUN sudo chmod 775 /home/gnuhealth/start_gnuhealth.sh

# RUN sh . ${HOME}/.gnuhealthrc

# RUN cd $HOME \
#     sh ./start_gnuhealth.sh

# RUN sudo touch /etc/systemd/system/gnuhealth.service
# RUN sudo touch /lib/systemd/system/gnuhealth.service
# USER root
# RUN echo -e "[Unit]\nDescription=GNU Health Server\nAfter=network.target\n\n[Service]\nType=simple\nUser=gnuhealth\nWorkingDirectory=/home/gnuhealth\nExecStart=/home/gnuhealth/start_gnuhealth.sh\nRestart=on-abort\n\n[Install]\nWantedBy=multi-user.target" >> /etc/systemd/system/gnuhealth.service
# RUN echo -e "[Unit]\nDescription=GNU Health Server\nAfter=network.target\n\n[Service]\nType=simple\nUser=gnuhealth\nWorkingDirectory=/home/gnuhealth\nExecStart=/home/gnuhealth/start_gnuhealth.sh\nRestart=on-abort\n\n[Install]\nWantedBy=multi-user.target" >> /lib/systemd/system/gnuhealth.service

# RUN service gnuhealth.service start





# ## Descargar desde repositorio, verificar y descomprimir
# RUN sudo chown -R gnuhealth $HOME
# RUN wget "https://ftp.gnu.org/gnu/health/gnuhealth-latest.tar.gz" \
#     && gpg --recv-key  --keyserver  keyserver.ubuntu.com 0xC015E1AE00989199 \
#     && gpg --with-fingerprint --list-keys 0xC015E1AE00989199 \
#     && wget https://ftp.gnu.org/gnu/health/gnuhealth-4.0.3.tar.gz.sig \
#     && gpg --verify -n gnuhealth-4.0.3.tar.gz.sig gnuhealth-latest.tar.gz \
#     && sudo tar xzf gnuhealth-latest.tar.gz 

#WORKDIR /home/gnuhealth/gnuhealth-4.0.4/

# ## Descargar el instalador desde repositorio e instalar
# RUN wget -O- https://ftp.gnu.org/gnu/health/gnuhealth-setup-latest.tar.gz | sudo tar -xzvf - \
#     && bash ./gnuhealth-setup install
