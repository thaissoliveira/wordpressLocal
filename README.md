# Como subir o WordPress localmente usando Docker Compose e o servidor Nginx?
## Pré Requisitos

### 1) Antes de tudo, como uso WSL, conectei meu VsCode ao WSL2.  

![image](https://github.com/user-attachments/assets/d117ae7f-069a-47d7-9dd0-5abd83493a7d)

### 2) Após isso, abri a pasta que criei no meu ambiente Linux Ubuntu 20.04 chamada "meu-wordpress" e criei a seguinte estrutura de arquivos e diretórios:

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

- A porta 8082 do host está mapeada para a porta 80 do contêiner.
- A linha 30 se refere a imagem oficial do WordPress, versão mais recente (latest), disponível no Docker Hub. Importante sempre usar imagens oficiais disponíveis no Docker Hub para os serviços.
- WORDPRESS_DB_HOST se refere ao endereço e porta do banco de dados.
- Na linha 41 defini um volume que mapeia dados persistentes do WordPress.
- Na linha 67 é exposta a porta 80 do container na porta 8082 do host, permitindo acesso ao site via http://localhost:8082.

## Arquivo nginx/default.conf

Em seguida, estruturei o arquivo de configuração do Nginx para servir o WordPress:

```
server {
    listen 80;
    server_name localhost;

    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ~ /\.ht {
        deny all;
    }
}

```
Pontos que acho importante ressaltar:

- O servidor está configurado para ouvir requisições da porta 80.
- O caminho root /var/www/html define o diretório base onde os arquivos do site estão localizados.



Após isso, vá até o diretório criado para seu projeto e execute o ```docker compose up -d"``` para subir os serviços!

Agora é só acessar o WordPress pelo seu navegador (no meu caso, usei ```http://localhost:8082```) e voilà, WordPress funcionando localmente!

![image](https://github.com/user-attachments/assets/668b93ec-24e6-4a0b-be2e-badbfd676e2e)


