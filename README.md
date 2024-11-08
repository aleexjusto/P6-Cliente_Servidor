# P6-Cliente_Servidor

Primeiro, debemos instalar os arquivos que temos no repositorio de damian, principalmente os arrquivos de configuración e de zonas. Unha vez feito, debemos configurar o documento .yml, que no meu caso quedou da seguinte manera.

```
services:
  bind9:   #servidor
    container_name: alexserver
    image: internetsystemsconsortium/bind9:9.18
    platform: linux/amd64
    ports:
      - 54:53/tcp #porto tcp
      - 54:53/udp #porto udp
    networks:
      alex_subnet:
        ipv4_address: 172.29.8.1  #ip definida chamada alex_subnet
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
    restart: always
  cliente:   #cliente
    container_name: cliente
    image: alpine
    platform: linux/amd64
    tty: true
    stdin_open: true
    # configuramos para que el cliente use nuestro dns
    dns:
      - 172.29.8.1
    networks:
      alex_subnet:
        ipv4_address: 172.29.8.2
        
networks:
  alex_subnet:
    driver: bridge
    ipam:
      config:
        - subnet: 172.29.0.0/16
          ip_range: 172.29.8.0/24
          gateway: 172.29.8.254
          
```
Como observamos, no noso servidor chamado alexserver fixen o seguinte: empregamos una imaxe bind9.18; definin os portos tcp e udp; asignei unha red personalizada que eu definin,neste caso 172.29.8.2; e engadimos os volumes, que son os arquivos que descargamos do repositorio de damian. 

No cliente, definimoslle una imaxe alpine, ademáis coas opciones 'tty' e 'stdin_open' permitimos que teña unha terminal, e finalemnte definimos a red DNS coa IP do servidor e definimos a IP fixa do cliente, neste caso 172.29.8.2.

Finalemente, definin a red empregada, neste caso como tipo bridge, e fixen o seguinte: definin o rango de Ip en 172.29.8.0/26, cpa ip_range limite as IP asignables a un rango específico, neste caso 172.29.8.0/24; e por ultimo definin a IP gateway en 172.29.8.254. Se creo a rede desde a terminal, neste apartado poñería 'externa:true'.
