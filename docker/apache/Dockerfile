FROM dev.registry.cartegriseminute.net/base_image_apache:1.0

# Copy apache vhost file to proxy php requests to php-fpm container
COPY config.apache.conf /usr/local/apache2/conf/config.apache.conf
RUN echo "Include /usr/local/apache2/conf/config.apache.conf" \
    >> /usr/local/apache2/conf/httpd.conf

# verify apache active module
RUN httpd -M
