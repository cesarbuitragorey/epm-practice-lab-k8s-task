# Imagen base explícita (evita warning hadolint)
FROM python:3.10-slim

# Evita buffering (mejor logs)
ENV PYTHONUNBUFFERED=1

# Directorio de trabajo
WORKDIR /app

# Copiar requirements primero (cache optimizado)
COPY requirements.txt .

# Instalar dependencias
RUN pip install --no-cache-dir -r requirements.txt

# Copiar código
COPY . .

# Exponer puerto correcto
EXPOSE 5000

# Comando de inicio
CMD ["python", "app.py"]