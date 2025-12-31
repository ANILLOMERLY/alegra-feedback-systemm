<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Feedback - Alegra</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .container {
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            max-width: 600px;
            width: 100%;
            padding: 40px;
        }

        .logo {
            text-align: center;
            margin-bottom: 30px;
        }

        .logo h1 {
            color: #667eea;
            font-size: 2.5em;
            font-weight: bold;
        }

        .logo p {
            color: #666;
            margin-top: 10px;
        }

        .form-group {
            margin-bottom: 25px;
        }

        label {
            display: block;
            color: #333;
            font-weight: 600;
            margin-bottom: 8px;
            font-size: 14px;
        }

        input[type="text"],
        select,
        textarea {
            width: 100%;
            padding: 12px 15px;
            border: 2px solid #e0e0e0;
            border-radius: 10px;
            font-size: 14px;
            transition: all 0.3s ease;
            font-family: inherit;
        }

        input[type="text"]:focus,
        select:focus,
        textarea:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        textarea {
            resize: vertical;
            min-height: 120px;
        }

        select {
            cursor: pointer;
            background-color: white;
        }

        .required {
            color: #e74c3c;
        }

        .optional {
            color: #95a5a6;
            font-weight: normal;
            font-size: 12px;
        }

        button {
            width: 100%;
            padding: 15px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: transform 0.2s ease, box-shadow 0.2s ease;
        }

        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(102, 126, 234, 0.3);
        }

        button:active {
            transform: translateY(0);
        }

        button:disabled {
            background: #ccc;
            cursor: not-allowed;
            transform: none;
        }

        .success-message {
            display: none;
            background: #2ecc71;
            color: white;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            text-align: center;
        }

        .error-message {
            display: none;
            background: #e74c3c;
            color: white;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            text-align: center;
        }

        .char-count {
            text-align: right;
            font-size: 12px;
            color: #95a5a6;
            margin-top: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">
            <h1>🎉 Alegra</h1>
            <p>Tu opinión nos ayuda a mejorar</p>
        </div>

        <div class="success-message" id="successMessage">
            ✅ ¡Gracias por tu feedback! Tu opinión ha sido registrada exitosamente.
        </div>

        <div class="error-message" id="errorMessage">
            ❌ Hubo un error al enviar tu feedback. Por favor, intenta nuevamente.
        </div>

        <form id="feedbackForm">
            <div class="form-group">
                <label for="userName">
                    Nombre <span class="optional">(opcional)</span>
                </label>
                <input 
                    type="text" 
                    id="userName" 
                    placeholder="Ingresa tu nombre"
                    maxlength="100"
                >
            </div>

            <div class="form-group">
                <label for="producto">
                    Producto <span class="required">*</span>
                </label>
                <select id="producto" required>
                    <option value="">Selecciona un producto</option>
                    <option value="Alegra POS">Alegra POS</option>
                    <option value="Alegra Contabilidad">Alegra Contabilidad</option>
                    <option value="Alegra Nómina">Alegra Nómina</option>
                </select>
            </div>

            <div class="form-group">
                <label for="comentario">
                    Comentario / Feedback <span class="required">*</span>
                </label>
                <textarea 
                    id="comentario" 
                    placeholder="Cuéntanos tu experiencia, sugerencias o comentarios..."
                    required
                    maxlength="1000"
                ></textarea>
                <div class="char-count">
                    <span id="charCount">0</span> / 1000 caracteres
                </div>
            </div>

            <button type="submit" id="submitBtn">
                Enviar Feedback
            </button>
        </form>
    </div>

    <script>
        // CONFIGURACIÓN: Reemplaza esta URL con la de tu Google Apps Script
        const SCRIPT_URL = 'TU_URL_DE_GOOGLE_APPS_SCRIPT_AQUI';

        const form = document.getElementById('feedbackForm');
        const comentario = document.getElementById('comentario');
        const charCount = document.getElementById('charCount');
        const submitBtn = document.getElementById('submitBtn');
        const successMessage = document.getElementById('successMessage');
        const errorMessage = document.getElementById('errorMessage');

        // Contador de caracteres
        comentario.addEventListener('input', function() {
            charCount.textContent = this.value.length;
        });

        // Manejo del envío del formulario
        form.addEventListener('submit', async function(e) {
            e.preventDefault();

            const userName = document.getElementById('userName').value.trim() || 'Anónimo';
            const producto = document.getElementById('producto').value;
            const comentarioText = comentario.value.trim();

            if (!producto || !comentarioText) {
                showError('Por favor completa todos los campos obligatorios.');
                return;
            }

            // Deshabilitar botón durante el envío
            submitBtn.disabled = true;
            submitBtn.textContent = 'Enviando...';

            try {
                const response = await fetch(SCRIPT_URL, {
                    method: 'POST',
                    mode: 'no-cors',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        userName: userName,
                        producto: producto,
                        comentario: comentarioText,
                        timestamp: new Date().toISOString()
                    })
                });

                // Con mode: 'no-cors', asumimos éxito si no hay error
                showSuccess();
                form.reset();
                charCount.textContent = '0';

            } catch (error) {
                console.error('Error:', error);
                showError('Hubo un error al enviar tu feedback. Por favor, intenta nuevamente.');
            } finally {
                submitBtn.disabled = false;
                submitBtn.textContent = 'Enviar Feedback';
            }
        });

        function showSuccess() {
            successMessage.style.display = 'block';
            errorMessage.style.display = 'none';
            setTimeout(() => {
                successMessage.style.display = 'none';
            }, 5000);
        }

        function showError(message) {
            errorMessage.textContent = '❌ ' + message;
            errorMessage.style.display = 'block';
            successMessage.style.display = 'none';
            setTimeout(() => {
                errorMessage.style.display = 'none';
            }, 5000);
        }
    </script>
</body>
</html>
