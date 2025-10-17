# Aplicación de bash para operaciones de confidencialidad con openSSL

## Descripción:
Esta aplicación permite gracias a openSSL y a zenity al ejecutarse tener una interfaz facil de usar para administrar, crear, encriptar y desencriptar archivos en linux usando las claves creadas, además de crear una ubicación para guardar:

## Requisitos
- OpenSSL instalado
- Zenity para los menús gráficos
- Sistema Linux o WSL en Windows

### Estructura del proyecto

```
Entrega-script-confindencialidad/
│
├─ Script        # Menú principal con Zenity
├─ script1       # Generación de claves RSA
├─ script2       # Cifrado y descifrado simétrico
├─ script3       # Cifrado y descifrado híbrido (AES+RSA)
├─ script4       # Gestión de claves públicas (ver, buscar, importar, exportar)
└─ README.md
```
## Instalación


1. Clonar el repositorio:
```
git clone https://github.com/javieresterasoraa/Entrega-script-confindencialidad.git
cd ./Entrega-script-confindencialidad
```
2. Dar permisos de ejecución a los scripts:
```
chmod +x *.sh
```
3. Ejecutar el menú principal:
```
./Script
```
## Menú
Al ejecutar menu.sh, se muestra un menú gráfico con las siguientes opciones:

Generar claves RSA
- Escoge el tamaño (2048 o 4096 bits)
- Introduce un nombre para los archivos de las claves
- Selecciona la carpeta donde se guardarán
- Se generan dos archivos:
  - <nombre>_privada.pem → clave privada
  - <nombre>_publica.pem → clave pública

Cifrado/descifrado simétrico
- Generar clave random: crea un archivo .bin de 32 bytes
- Cifrar archivo: usa AES-256-CBC con -pbkdf2 y -salt
- Descifrar archivo: requiere la misma clave .bin

Ejemplo:
1. Generar key.bin
2. Cifrar archivo.txt → archivo.enc
3. Descifrar archivo.enc → archivo_descifrado.txt

Cifrado/descifrado asimétrico híbrido
- Cifra archivos con AES-256 y cifra la clave AES con RSA
- Para cifrar:
  1. Selecciona archivo a cifrar
  2. Selecciona clave AES .bin
  3. Selecciona clave pública RSA .pem
  4. Define carpeta y nombre de salida
- Se generan:
  - <archivo>.enc → datos cifrados AES
  - <archivo>.aes.enc → clave AES cifrada con RSA
- Para descifrar:
  1. Selecciona los archivos .enc y .aes.enc.
  2. Selecciona clave privada RSA.
  3. Selecciona carpeta y nombre de salida.
(El IV se genera automáticamente dentro del archivo AES, no necesitas gestionarlo manualmente.)

Gestión de claves públicas
- Ver clave: muestra el contenido legible de una clave pública.
- Buscar claves: busca archivos con patrones *.pem, *.pub o *_public.pem en la carpeta escogida.
- Importar clave: copia la clave seleccionada al directorio keyring/ con permisos 600.
- Exportar clave: permite guardar la clave desde keyring/ a otra ruta.

## Cifrado híbrido

El cifrado híbrido combina simétrico y asimétrico:

1. AES-256-CBC cifra el archivo original usando una clave aleatoria de 32 bytes.
2. La clave AES se cifra con RSA para protegerla.
3. El destinatario usa su clave privada RSA para descifrar la clave AES, y luego usa esa clave para descifrar los datos.
