# REGISTRO DE INCIDENCIAS Y COMPROBACIONES DE INSTALACIÓN

En este documento detallaremos las comprobaciones preventivas al despliegue de Odoo con Docker Compose, también el resgistro y la resolucion de la indicencia que tuve en el primer arranque del sistema

## 1. Comprobaciones Preventivas Realizadas

Para que hubiese todos los errores minimos que podíamos encontrar en la instalacion del archivo 'docker-compose.yml', hivimos antes una validaciones en Windows antes de ejecutar el comando:
* **Verificación del motor Docker:** Comprobamos que Docker Desktop esté abierto y funcionara con normalidad, así evitabamos que la terminal diera errores de conexión con el demonio de Docker
* **Disponibilidad de Puertos:** Verificamos que ningún otro servicio local estuviera ocupando los puertos `8069` y `8200`, para evitarmos el error 'port is already allocated'
* **Estructura de Directorios:** Se confirmó que la carpeta `docker odoo` solo tuviera el archivo `.yml` y que no había nada de instalaciones anteriores

## 2. Tabla Resumen de Incidencias

| Lo que pasó | Por qué pasó | Cómo lo arreglé | Fuente consultada |
| :--- | :--- | :--- | :--- |
| **Se quedó la pantalla totalmente pillada (congelada) justo al meter las credenciales.** | Hubo un fallo en la primera carga y, como Docker guarda los datos, el error se quedó guardado en el volumen. Un reinicio normal no servía. | Borrar los contenedores y los volúmenes enteros desde la terminal para hacer una instalación totalmente limpia desde cero. | *Foros de la comunidad de Odoo y documentación de Docker sobre volúmenes.* |

## 3. Mi error real y cómo lo solucioné paso a paso

Durante la práctica me pasó algo que es bastante molesto, que cuando has esperado a que te instale y ua estas en el ultimo paso se te quede colgado. Justo cuando to parecía que iba a ir bien, metí las credenciales y se me quedó pillado, por mucho que cerrara, apagara el contenedor y reiniciara, se me abría en el puerto la misma página colgada
Así fue como lo arreglé:

### ¿Qué ocurrió exactamente?
Puse el comando `docker-compose up -d` en la terminal, los contenedores se instalaron y los abri en el navegador. Se abrió la pantalla principal de Odoo, pero justo cunado metí las credenciales y darla entrar, **se abrió otra pantalla y se quedo pillada**

Nada cargaba. Probé a cerrar la pestaña y abrirla de nuevo, pero seguía igual, no cambiaba nada. Fui a Docker Desktop, paré los contenedores y los volví a arrancar, pero al meterme denuevo en la web seguía estando congelada en el mismo sitio.

## ¿Por qué pasaba esto?
Docker guarda la información de la base de datos en lo que se llaman "volúmenes" para que no pierdad tus cosas al apagar el ordenador. El problema es que, como se quedó pillado justo en la primera carga, ese fallo se guardó en el volumen. Por eso, aunque yo reiniciara los contenedores cien veces, Odoo volía a leer esa misma base dedatos corrupta y se volvía a quedar colgado.

## ¿Cómo lo arreglé?
Como probé a reiniciar y no sirvió de nada, tuve que borrar toda la raíz y volver a empezar.

**1. Borrar todo (incluso los volúmenes)**
Abrí la terminar en mi carpeta 'docker odoo' y puse este comando para cargarme los contenedores y los volúmenes (`-v`) que daban fallo:
```bash
docker-compose down -v
```

**2. Revisar que Docker estuviera limpio**
Me metí en la aplicació de Docker Desktop para asegururarme de que no quedaba más "pillado":
* En **Containers**, vi que ya no estaban los de Odoo
* En **Volumenes**, comproé que habían desaparecido los volúmenes `odoo-web-data` y `odoo-db-data`

**3. Volver a instalar**
Una vez estaba todo limpio, volví a la terminal y metí de nuevo el comando para levantarlo:
```bash
docker-compose up -d
```
Esta vez lo dejé un ratito más para segurarme de que a la base de datos le daba tiempo a cargar bien todo por detrás.

**4. Comprobación final**
Volví a entrar en el navegador, metí mis credenciales y esta vez sí, me dejó entrar del tirón y súper fluido. A partir de ahí ya pude seguir con la práctica y configurar todo lo que me pedía.