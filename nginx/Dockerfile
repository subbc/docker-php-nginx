FROM alpine:3.6

LABEL maintainer="NGINX Docker Maintainers <wuxiaobing@eelly.net>"
ENV NGINX_VERSION=1.14.0
	
RUN GPG_KEYS=B0F4253373F8F6F510D42178520A9993A1C052F8 \
	&& CONFIG="\
		--prefix=/etc/nginx \
		--sbin-path=/usr/sbin/nginx \
		--modules-path=/usr/lib/nginx/modules \
		--conf-path=/etc/nginx/nginx.conf \
		--error-log-path=/var/log/nginx/error.log \
		--http-log-path=/var/log/nginx/access.log \
		--pid-path=/var/run/nginx.pid \
		--lock-path=/var/run/nginx.lock \
		--http-client-body-temp-path=/var/cache/nginx/client_temp \
		--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
		--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
		--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
		--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
		--user=www --group=www \
		--with-poll_module --with-threads --with-file-aio \
		--with-http_ssl_module --with-http_v2_module \
		--with-http_realip_module --with-http_addition_module \
		--with-http_xslt_module --with-http_image_filter_module \
		--with-http_sub_module --with-http_dav_module \
		--with-http_flv_module --with-http_mp4_module \
		--with-http_gunzip_module --with-http_gzip_static_module \
		--with-http_auth_request_module --with-http_random_index_module \
		--with-http_secure_link_module --with-http_degradation_module \
		--with-http_slice_module --with-http_stub_status_module \
		--with-stream --with-stream_ssl_module \
		--with-stream_ssl_preread_module --with-stream_realip_module \
		--with-compat --with-pcre --with-pcre-jit \
		--add-module=../echo-nginx-module --add-module=../headers-more-nginx-module \
		--add-module=../nginx-rewrite-request-body-module --add-module=../ngx_devel_kit \
		--add-module=../ngx_http_qrcode_module --add-module=../ngx-http-status-code-counter \
		--add-module=../set-misc-nginx-module --add-module=../srcache-nginx-module \
	" \
	&& apk add --no-cache --virtual .build-deps \
		gcc g++ \
		libc-dev \
		make \
		openssl-dev \
		pcre-dev \
		zlib-dev \
		linux-headers \
		curl \
		gnupg \
		libxslt-dev \
		gd-dev libqrencode-dev git \
		\
	&& mkdir -p /usr/src \
	&& cd /usr/src \
	&& addgroup -S www \
	&& adduser -D -S -h /var/cache/nginx -s /sbin/nologin -G www www \
	&& curl -fSL http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz -o nginx.tar.gz \
	&& curl -fSL http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz.asc  -o nginx.tar.gz.asc \
	&& export GNUPGHOME="$(mktemp -d)" \
	&& found=''; \
	for server in \
		ha.pool.sks-keyservers.net \
		hkp://keyserver.ubuntu.com:80 \
		hkp://p80.pool.sks-keyservers.net:80 \
		pgp.mit.edu \
	; do \
		echo "Fetching GPG key $GPG_KEYS from $server"; \
		gpg --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$GPG_KEYS" && found=yes && break; \
	done; \
	test -z "$found" && echo >&2 "error: failed to fetch GPG key $GPG_KEYS" && exit 1; \
	gpg --batch --verify nginx.tar.gz.asc nginx.tar.gz \
	&& rm -rf "$GNUPGHOME" nginx.tar.gz.asc \
	&& tar -zxC /usr/src -f nginx.tar.gz \
	&& rm -rf nginx.tar.gz \
	&& git clone https://github.com/openresty/echo-nginx-module.git \
	&& git clone https://github.com/openresty/headers-more-nginx-module.git \
	&& git clone https://github.com/tszming/nginx-rewrite-request-body-module.git \
	&& git clone https://github.com/simplresty/ngx_devel_kit.git \
	&& git clone https://github.com/dcshi/ngx_http_qrcode_module.git \
	&& git clone https://github.com/qch-china/ngx-http-status-code-counter.git \
	&& git clone https://github.com/magicbear/ngx_realtime_request_module.git \
	&& git clone https://github.com/openresty/set-misc-nginx-module.git \
	&& git clone https://github.com/openresty/srcache-nginx-module.git \
	&& cd /usr/src/nginx-${NGINX_VERSION} \
	&& ./configure $CONFIG --with-debug \
	&& make -j$(getconf _NPROCESSORS_ONLN) \
	&& mv objs/nginx objs/nginx-debug \
	&& ./configure $CONFIG \
	&& make -j$(getconf _NPROCESSORS_ONLN) \
	&& make install \
	&& rm -rf /etc/nginx/html/ \
	&& mkdir /etc/nginx/conf.d/ \
	&& mkdir -p /data/web/default /data/logs \
	&& chmod -R 777 /data/logs \
	&& install -m644 html/index.html /data/web/default \
	&& install -m644 html/50x.html /data/web/default \
	&& strip /usr/sbin/nginx* \
	&& cd / \
	&& rm -rf /usr/src \
	&& apk add --no-cache --virtual .gettext gettext \
	&& mv /usr/bin/envsubst /tmp/ \
	\
	&& runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' /usr/sbin/nginx  /tmp/envsubst \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	)" \
	&& apk add --no-cache --virtual .nginx-rundeps $runDeps \
	&& apk del .build-deps \
	&& apk del .gettext \
	&& mv /tmp/envsubst /usr/local/bin/ \
	\
	# Bring in tzdata so users could set the timezones through the environment
	# variables
	&& apk add --no-cache tzdata \
	\
	# forward request and error logs to docker log collector
	&& ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

COPY nginx.conf eelly.dev.* server.ssl.conf /etc/nginx/
COPY conf.d/* /etc/nginx/conf.d/
##COPY p.php  /data/web/default/

EXPOSE 80
EXPOSE 443

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
