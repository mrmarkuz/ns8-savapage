FROM debian:bookworm
 
RUN apt update && apt install --no-install-recommends --no-install-suggests -y binutils cpio \
    cups cups-bsd debianutils default-jdk-headless gzip imagemagick librsvg2-bin perl poppler-utils \
    qpdf supervisor wkhtmltopdf libheif-examples vim-tiny findutils apt-utils iputils-ping \
    gnupg curl hplip
 
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY docker.env /etc/environment
 
RUN useradd -rmd /opt/savapage -s /bin/bash -G lpadmin savapage && chown savapage:savapage /opt/savapage
# Create persistent password for admin tasks in CUPS web interface.
RUN echo 'savapage:savapage' | chpasswd
 
ENV SAVAPAGE_VERSION=1.6.0-rc
ENV SAVAPAGE_NS=SP_
ENV SP_CONTAINER=DOCKER
 
USER savapage
COPY ./savapage-setup-${SAVAPAGE_VERSION}-linux-x64.bin /opt/savapage/savapage-setup.bin
RUN ["bash", "/opt/savapage/savapage-setup.bin", "-n"]
USER root
RUN ["/opt/savapage/server/bin/linux-x64/roottasks", "pam"]
 
EXPOSE 631 8631 8632
 
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]