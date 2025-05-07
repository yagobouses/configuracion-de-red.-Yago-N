# 🖥️ Configuración de Red - Yago N

## 1. Adaptadores

Ponemos dos adaptadores:

- El primero en **NAT**.
- El segundo en **Red Interna** (`intnet`).

![lsblk](/capturas/1.png)
![lsblk](/capturas/2.png)
---

## 2. IP Máquina A y B

- Comprobamos los adaptadores con `ip a`.
- Identificamos `enp0s8` como la interfaz de red interna.
- Asignamos IPs estáticas (de forma no permanente) a cada máquina:

### En Máquina A:
```bash
sudo ip addr add 192.168.100.2/24 dev enp0s8
sudo ip link set enp0s8 up
```
![lsblk](/capturas/3.png)
### En Máquina B:
```bash
sudo ip addr add 192.168.100.3/24 dev enp0s8
sudo ip link set enp0s8 up
```

![lsblk](/capturas/4.png)
---

## 3. Ping

### Desde Máquina A a Máquina B:
```bash
ping 192.168.100.3
```

### Desde Máquina B a Máquina A:
```bash
ping 192.168.100.2
```

![lsblk](/capturas/5.png)
![lsblk](/capturas/6.png)

---

## 4. Configuración persistente en Máquina A (Netplan)

Editamos el archivo Netplan:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

Contenido:
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      addresses:
        - 192.168.100.2/24
```
![lsblk](/capturas/7.png)
Guardamos y aplicamos con:
```bash
sudo netplan apply
```

![lsblk](/capturas/8.png)

---

## 5. Configuración persistente en Máquina B (NetworkManager)

1. Borramos configuración previa (opcional):
```bash
nmcli con delete enp0s8
```

2. Creamos una nueva conexión:
```bash
nmcli con add type ethernet ifname enp0s8 con-name intnet-B ip4 192.168.100.3/24
nmcli con modify intnet-B ipv4.method manual
nmcli con up intnet-B
```
![lsblk](/capturas/9.png)
3. Verificamos:
```bash
ip a
```

![lsblk](/capturas/10.png)

---

## ✅ Verificación Final

Después de reiniciar, se comprueba que las IPs persisten y que las máquinas siguen comunicándose.

```bash
ping 192.168.100.3  # Desde Máquina A
ping 192.168.100.2  # Desde Máquina B
```
![lsblk](/capturas/11.png)
![lsblk](/capturas/12.png)
