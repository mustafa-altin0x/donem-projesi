FROM httpd:2.4.50

RUN echo "ODEV_TAMAMDIR_BAYRAGI_BULDUN" > /secret.txt

RUN sed -i 's/#LoadModule cgid_module/LoadModule cgid_module/g' /usr/local/apache2/conf/httpd.conf
RUN sed -i 's/#LoadModule cgi_module/LoadModule cgi_module/g' /usr/local/apache2/conf/httpd.conf

RUN sed -i 's/Require all denied/Require all granted/g' /usr/local/apache2/conf/httpd.conf

EXPOSE 80
