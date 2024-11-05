# P6-Cliente_Servidor


services:
  bind9:
    container_name: alexserver
    # usamos esta imagen para poder ver los logs
    # la imagen de internetsystemsconsortium/bind9
    # no muestra los logs
    image: internetsystemsconsortium/bind9:9.18
    platform: linux/amd64
    ports:
      - 54:53/tcp
      - 54:53/udp
    networks:
      alex_subnet:
        ipv4_address: 172.29.8.1
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
    restart: always
  cliente:
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
