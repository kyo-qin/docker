FROM centos:centos7

# 从centos基础镜像构建

# 设置工作目录
WORKDIR "/tmp"

# 这里远程下载太慢了 我直接本地弄得。大家可以使用wget来进行远程下载
ADD nginx-1.20.1.tar.gz /tmp

# 添加nginx用户
RUN useradd -M -s /sbin/nologin nginx

# 安装相关依赖
RUN  yum -y install gcc*  make pcre-devel zlib-devel openssl openssl-devel libxslt-devel gd gd-devel GeoIP GeoIP-devel pcre pcre-devel \
    && cd nginx-1.20.1 \
    && ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx --with-file-aio --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_image_filter_module --with-http_geoip_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_auth_request_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_stub_status_module && make && make install \
    && echo "hello qintao.com" > /usr/local/nginx/html/index.html

# 表示对外期望暴露得端口
EXPOSE 80

# 启动nginx 将nginx主进程 pid为1 nginx一旦挂掉那么docker容器就会直接退出
CMD ["/usr/local/nginx/sbin/nginx", "-g", "daemon off;"]
