# 教程来源
```
https://guides.wp-bullet.com/blocking-country-and-continent-with-nginx-geoip-on-ubuntu-18-04/
```

# 1
```
sudo apt-get install geoip-database-extra libgeoip1 libnginx-mod-http-geoip -y
```

# 2
避免编译了，牛逼。
```
cd /usr/share/GeoIP
mv GeoIP.dat GeoIP.dat.bak
wget https://dl.miyuru.lk/geoip/maxmind/country/maxmind.dat.gz
gunzip maxmind.dat.gz
mv maxmind.dat GeoIP.dat
mv GeoIPCity.dat GeoIPCity.dat.bak
wget https://dl.miyuru.lk/geoip/maxmind/city/maxmind.dat.gz
gunzip maxmind.dat.gz
mv maxmind.dat GeoIPCity.dat
```

# 3
`http`配置块中：
```
geoip_country /usr/share/GeoIP/GeoIP.dat;
geoip_city /usr/share/GeoIP/GeoIPCity.dat;
```
比如：
```
#WP-Bullet.com nginx configuration
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        multi_accept on;
}

http {
    # GeoIP databases
    geoip_country /usr/share/GeoIP/GeoIP.dat;
    geoip_city /usr/share/GeoIP/GeoIPCity.dat;
    ...

```
# 4
也配置到`http`块中：
```
# map the list of denied countries
map $geoip_country_code $allowed_country {
   default yes;
   # Pakistan
   PK no;
   # Ukraine
   UA no;
   # Russia
   RU no;
   # China
   CN no;
   }
```

# 5 
在自己的虚拟机配置中：
```
# block the country
if ($allowed_country = no) {
    return 403;
}
```
