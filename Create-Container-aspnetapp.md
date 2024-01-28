# Vamos criar um container simples (sem AKS) com o .NET

Para este lab, é necessário ter o docker já previamente instalado.


Abrimos a bash e executamos o comando que irá criar o container. Se a imagem não existir localmente, o docker descarrega-a do registo indicado:

```
docker run -d --name aspnet001 mcr.microsoft.com/dotnet/samples:aspnetapp

```


Entrar no container e verificar que portas estão à escuta e o IP do container:
```
docker exec -it aspnet001 sh

netstat -tulpn

ip a
```


Com o comando docker também é possivel verificar a porta à escuta no container:

```
$ docker ps

CONT.ID  IMAGE                                       COMMAND        CREATED        STATUS        PORTS     NAMES
4bae247  mcr.microsoft.com/dotnet/samples:aspnetapp  "./aspnetapp"  4 seconds ago  Up 3 seconds  8080/tcp  aspnet001

```

Se tentarem aceder ao container com o curl ou via navegador de internet ainda não irão conseguir aceder à pagina asp, pois o container não foi criado com redirecionamento de portas.

Vamos voltar a criar o container, desta vez, redirecionando a porta 80 do host (máquina on está instalado o docker) para a porta 8080 do container:

```
docker run -d --name aspnet002 -p 80:8080 mcr.microsoft.com/dotnet/samples:aspnetapp
```

Agora já podem testar com o curl ou com um navegador de internet:

```
curl 127.0.0.1:80
```

Outros comandos:

```
docker ps
docker ps -a
docker stop aspnet001
docker start aspnet001
```


Vamos remover os containers. Com o parâmetro -f consegue-se eliminar o container mesmo estanto a correr.

```
docker rm aspnet001
docker rm -f aspnet001
```

Remover também a imagem descarregada:

```
docker images

docker image rm IMAGE_ID
```


# Créditos

https://learn.microsoft.com/en-gb/training/modules/intro-to-containers/2-deploy-docker-image-locally






