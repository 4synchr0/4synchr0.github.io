<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Galería de la Comunidad</title>
    <!-- Importación del cliente SDK v2 de Supabase -->
    <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
    <style>
        body { font-family: sans-serif; background: #121212; color: white; padding: 20px; text-align: center; }
        .galeria { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
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
        const supabaseUrl = 'https://gfduytniqrtlhneryjwc.supabase.co';
        const supabaseKey = 'sb_publishable_Y3aHX99m0fIhmgsw-Lu_pA_-9q6NYh9';
        const bucketName = 'Lienzo_imagenes_v2';

        // Se usa `supabaseClient` para evitar conflicto de nombres con la librería global `supabase`
        const supabaseClient = supabase.createClient(supabaseUrl, supabaseKey);
        const contenedor = document.getElementById('contenedor-galeria');
        const estado = document.getElementById('estado');

        async function cargarGaleria() {
            // Consulta de lista de archivos
            const { data: archivos, error } = await supabaseClient.storage.from(bucketName).list();

            if (error) {
                estado.innerText = "Error al conectar con Supabase.";
                console.error(error);
                return;
            }

            if (!archivos || archivos.length === 0) {
                estado.innerText = "Aún no hay imágenes subidas.";
                return;
            }

            estado.style.display = "none";

            archivos.forEach(archivo => {
                if (archivo.name === ".emptyFolderPlaceholder") return;

                const { data } = supabaseClient.storage.from(bucketName).getPublicUrl(archivo.name);

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

        cargarGaleria();
    </script>
</body>
</html>
