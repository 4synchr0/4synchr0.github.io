<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Galería de la Comunidad</title>
    <!-- Importamos la herramienta de Supabase directamente de internet -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <style>
        body { font-family: sans-serif; background: #121212; color: white; padding: 20px; text-align: center; }
        .galeria { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); /* Cuadrícula automática */
            gap: 15px; 
            max-width: 1000px; 
            margin: 0 auto; 
        }
        .imagen-box { background: #222; padding: 10px; border-radius: 8px; }
        .imagen-box img { width: 100%; height: 200px; object-fit: cover; border-radius: 4px; }
        .nombre { margin-top: 10px; font-size: 12px; color: #aaa; word-break: break-all; }
    </style>
</head>
<body>

    <h1>Capturas de Lienzo Infinito</h1>
    <p id="estado">Conectando con el servidor...</p>
    
    <div id="contenedor-galeria" class="galeria"></div>

    <script>
        // 1. Pon aquí los datos de tu Supabase
        const supabaseUrl = 'https://gfduytniqrtlhneryjwc.supabase.co';
        const supabaseKey = 'sb_publishable_Y3aHX99m0fIhmgsw-Lu_pA_-9q6NYh9';
        const bucketName = 'Lienzo_imagenes_v2';

        // Inicializamos la conexión al servidor de Supabase
        const supabase = supabase.createClient(supabaseUrl, supabaseKey);
        const contenedor = document.getElementById('contenedor-galeria');
        const estado = document.getElementById('estado');

        async function cargarGaleria() {
            // 2. Le pedimos a Supabase la lista de todo lo que hay en el bucket
            const { data: archivos, error } = await supabase.storage.from(bucketName).list();

            if (error) {
                estado.innerText = "Error al conectar con Supabase.";
                console.error(error);
                return;
            }

            if (!archivos || archivos.length === 0) {
                estado.innerText = "Aún no hay imágenes subidas.";
                return;
            }

            estado.style.display = "none"; // Ocultamos el mensaje de carga

            // 3. Por cada archivo encontrado, creamos un cuadrito en la web
            archivos.forEach(archivo => {
                // Ignoramos archivos invisibles del sistema
                if (archivo.name === ".emptyFolderPlaceholder") return;

                // Armamos la URL pública de la imagen
                const { data } = supabase.storage.from(bucketName).getPublicUrl(archivo.name);

                // Creamos el HTML para mostrarla
                const box = document.createElement('div');
                box.className = 'imagen-box';
                box.innerHTML = `
                    <a href="${data.publicUrl}" target="_blank">
                        <img src="${data.publicUrl}" alt="${archivo.name}">
                    </a>
                    <div class="nombre">${archivo.name}</div>
                `;
                contenedor.appendChild(box);
            });
        }

        // Ejecutamos la función apenas se abre la web
        cargarGaleria();
    </script>
</body>
</html>
