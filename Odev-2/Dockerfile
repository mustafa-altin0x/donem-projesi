FROM httpd:2.4.50

# Senin istediğin o efsanevi bayrağı yazıyoruz
RUN echo "ODEV_TAMAMDIR_BAYRAGI_BULDUN" > /secret.txt

# RCE (Komut Çalıştırma) için gereken motorları KESİN olarak açıyoruz
RUN sed -i 's/#LoadModule cgid_module/LoadModule cgid_module/g' /usr/local/apache2/conf/httpd.conf
RUN sed -i 's/#LoadModule cgi_module/LoadModule cgi_module/g' /usr/local/apache2/conf/httpd.conf

# Dizin atlama (Path Traversal) engellerini tamamen kaldırıyoruz
RUN sed -i 's/Require all denied/Require all granted/g' /usr/local/apache2/conf/httpd.conf

EXPOSE 80