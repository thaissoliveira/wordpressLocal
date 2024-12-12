# Como subir o WordPress localmente usando docker compose e o servidor Nginx?
## Pré Requisitos
Antes de tudo, como uso WSL, conectei meu VsCode ao WSL2.  
![image](https://github.com/user-attachments/assets/d117ae7f-069a-47d7-9dd0-5abd83493a7d)

Após isso, abri a pasta que criei no meu ambiente Linux Ubuntu 20.04 chamada "meu-wordpress" e criei a seguinte estrutura de arquivos e diretórios:

```
wordpress-nginx-docker/
├── docker-compose.yml
├── nginx/
│   └── default.conf
└── data/
    └── db/
```

O arquivo docker-compose.yml é r
