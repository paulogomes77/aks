# Vamos criar um container simples (sem AKS) com o .NET

É necessário ter o docker já previamente instalado.

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

