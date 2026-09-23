# Vicerrectorado · Sucre

Panel estático de HTML, CSS y JavaScript para GitHub Pages. Compatible con `vicerectorfinal_github.py` y su protocolo `usfx-web-v1`. No requiere servidor, dominio comprado, CDN ni paquetes npm.

## Publicación inicial

1. Cree el repositorio **público** `vicerrectorado-sucre` en su cuenta de GitHub.
2. Suba el contenido de esta carpeta (no la carpeta contenedora) a la rama `main`.
3. Abra **Settings → Pages → Build and deployment → Deploy from a branch**.
4. Seleccione **main / (root)** y pulse **Save**.
5. Espere a que GitHub termine el despliegue. La dirección será `https://USUARIO.github.io/vicerrectorado-sucre/`.

Hasta el primer envío de Python el sitio muestra «Todavía no hay datos publicados». No hay contraseña predeterminada ni datos de demostración publicados.

## Conectar con Python

1. Ejecute su herramienta en la carpeta habitual, junto a `usfx_elecciones_data`.
2. Abra **8. Publicación web**.
3. Escriba `USUARIO/vicerrectorado-sucre`, rama `main`.
4. Configure un token específico de GitHub con acceso únicamente a este repositorio y permiso **Contents: Read and write**. Introduzca el token solamente en su herramienta local; nunca lo comparta con quienes consultan el panel.
5. Establezca una contraseña larga y aleatoria, repítala, guarde la configuración y pulse **Publicar ahora**.
6. Cuando Pages termine de actualizarse, abra el panel e introduzca esa contraseña.

Python reemplaza un único archivo: `data/dashboard.enc.json`. Cada actualización contiene una instantánea completa. El panel comprueba cambios cada minuto mientras está visible y ofrece un botón de actualización manual. Un error de red conserva la consulta en memoria. Apagar la computadora no elimina el archivo de GitHub. GitHub Pages puede tardar unos minutos en desplegar cada cambio.

## Seguridad

- El repositorio y la interfaz son públicos. Los resultados se descifran únicamente en el navegador con la contraseña. Esto no es autenticación de servidor ni un repositorio privado.
- PBKDF2-HMAC-SHA256, 600 000 iteraciones; AES-256-GCM; sal de 16 bytes, IV de 12 bytes y etiqueta de autenticación de 128 bits. AAD: `usfx-web-v1`.
- La contraseña, la clave y los datos descifrados no se guardan en localStorage, sessionStorage ni cookies. La página no envía la contraseña a ningún servidor. La sesión se cierra al recargar, al salir o tras aproximadamente 15 minutos sin actividad.
- Cambiar la contraseña en Python y publicar cifra la nueva versión con esa contraseña. Las copias antiguas del historial Git siguen protegidas por sus contraseñas antiguas. No se pueden retirar copias que alguien ya haya descargado.
- Cualquiera puede descargar el archivo cifrado e intentar adivinar la contraseña sin conexión. Use una contraseña aleatoria fuerte y compártala solo con sus lectores autorizados.
- Quien pueda modificar este repositorio puede cambiar el JavaScript. Limite los colaboradores con escritura. No suba su Python, archivos de datos originales, tokens ni claves de IA.
- El panel no contiene scripts remotos, analítica, fuentes externas ni dependencias CDN. Los enlaces a publicaciones solo se abren cuando el lector los selecciona.

## Datos y criterios de conteo

El archivo descifrado contiene `schema: usfx-data-v1`, `updated_at`, `tables` y `documents`. Las filas CSV conservan los valores como texto, incluidos los identificadores de 19 dígitos. El panel convierte solo las métricas, respetando la coma decimal. Nunca suma visualizaciones a interacciones.

- Conteo global por `post_key`, sin duplicar una publicación presente en varias tablas. La tabla propia tiene prioridad sobre una copia de escucha.
- Las relaciones de `puente_post_candidato.csv` atribuyen una mención a varios candidatos. Cada candidato recibe esa actividad en la comparativa, pero el total global solo la cuenta una vez. Por ello, la suma por candidato puede superar el total global.
- Las menciones sin candidato se incluyen al consultar «Todos los candidatos» y aparecen como «Sin asignar». Esto permite ver toda la escucha exportada, incluida la no asociada a candidatos.
- Se excluyen filas sin `post_key` y fechas no válidas o posteriores al día siguiente en UTC; el panel indica cuántas. No se inventan fechas para graficar.
- La vista inicial abarca todas las fechas válidas. Se puede filtrar por candidato, origen, red y fechas. Las series omiten días sin publicaciones. No se recalculan clasificaciones IA ni outliers.
- Si interaccion_total está vacío o en cero, se suman me_gusta, comentarios y compartidos. Los valores negativos o no numéricos se muestran como cero.
- Sentimiento y calidad no disponibles aparecen como «Sin clasificar». El FODA conserva el texto publicado por Python.
- La tabla ofrece todos los campos originales de cada publicación en su detalle.

Las cifras miden actividad digital, no intención de voto. Pueden diferir de una vista de Python si utiliza otros filtros, exclusión de outliers o suma repetida de menciones compartidas.

## Prueba local

Sirva esta carpeta mediante HTTP local (por ejemplo `python -m http.server 8000`) y abra `http://localhost:8000`. En producción use siempre HTTPS. No abra el HTML con `file://`.
