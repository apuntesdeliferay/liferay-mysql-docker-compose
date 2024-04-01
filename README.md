# Ejemplo de `docker-compose.yml` para Liferay + MySQL

Este es un ejemplo para ejecutar Liferay + MySQL desde Docker.

Incluye el archivo de configuración `context.xml` apuntando al servidor MySQL que está definido en el compose.

```xml
    <Resource
        name="jdbc/LiferayPool"
        auth="Container"
        type="javax.sql.DataSource"
        driverClassName="com.mysql.cj.jdbc.Driver"
        url="jdbc:mysql://mysql-liferay-server:3306/lportal?characterEncoding=UTF-8&amp;dontTrackOpenResources=true&amp;holdResultsOpenOverStatementClose=true&amp;serverTimezone=America/Lima&amp;useFastDateParsing=false&amp;useUnicode=true&amp;allowPublicKeyRetrieval=true&amp;useSSL=false"
        username="lportal"
        password="lportal"
        maxTotal="100"
        maxIdle="30"
        maxWaitMillis="10000"
    />
```

El nombre `mysql-liferay-server` es el nombre del container descrito en `docker-compose.yml`

Una nota importante, es que para ambos contenedores se vean, debe declararse las notaciones `networks`

```yml
version: '3.3'
services:
  mysql-server:
    image: mysql:latest
    container_name: 'mysql-liferay-server'
    volumes:
      - ./mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      TZ: America/Lima
      MYSQL_DATABASE: lportal
      MYSQL_USER: lportal
      MYSQL_PASSWORD: lportal
    ports:
      - 13306:3306
    networks:
      - liferaynet
  liferay:
    depends_on:
      - mysql-server
    image: liferay/portal:latest
    volumes:
      - ./liferay/deploy:/opt/liferay/deploy
      - ./liferay/conf/context.xml:/opt/liferay/tomcat/conf/context.xml
      - ./liferay/conf/portal-ext.properties:/opt/liferay/portal-ext.properties
    ports:
      - 8088:8080
    networks:
      - liferaynet
networks:
  liferaynet:
```    
