# Como subir o WordPress localmente usando Docker Compose e o servidor Nginx?
## Pré Requisitos

1) Antes de tudo, como uso WSL, conectei meu VsCode ao WSL2.  

![image](https://github.com/user-attachments/assets/d117ae7f-069a-47d7-9dd0-5abd83493a7d)

2) Após isso, abri a pasta que criei no meu ambiente Linux Ubuntu 20.04 chamada "meu-wordpress" e criei a seguinte estrutura de arquivos e diretórios:

```
wordpress-nginx-docker/
├── docker-compose.yml
├── nginx/
│   └── default.conf
└── data/
    └── db/
```

## Arquivo docker-compose.yml

O arquivo docker-compose.yml é um arquivo de configuração usado no Docker Compose para gerenciar múltiplos contêineres Docker como um serviço único. Ele descreve os contêineres, configurações de redes, volumes e variáveis de ambiente necessários para a execução de uma aplicação complexa que envolve mais de um contêiner. No caso desta atividade, o docker-compose.yml está gerenciando serviços para WordPress, servidor Nginx e banco de dados MySQL.

Usei o seguinte arquivo .yml:

```
version: '3.8'

services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    ports:
      - "8082:80" 
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress_data:/var/www/html
    networks:
      - wp_network

  db:
    image: mysql:5.7
    container_name: wordpress_db
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - wp_network

  nginx:
    image: nginx:latest
    container_name: wordpress_nginx
    restart: always
    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
      - wordpress_data:/var/www/html
    ports:
      - "8080:80"
    networks:
      - wp_network

volumes:
  wordpress_data:
  db_data:

networks:
  wp_network:
```

Pontos que acho importante ressaltar:

- Eu particularmente, ás vezes, me confundo com as portas, então, para ficar claro: a porta 8082 do host está mapeada para a porta 80 do contêiner.
-  
