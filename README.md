# Ollama Docker Compose Setup

Docker Compose configuration untuk menjalankan Ollama yang dapat diakses oleh host dan container lain.

## Fitur

- ✅ Ollama dapat diakses dari host melalui port `11434`
- ✅ Ollama dapat diakses oleh container lain melalui network `ollama_network`
- ✅ Data persisten menggunakan Docker volume
- ✅ Auto-restart jika container crash
- ✅ Konfigurasi GPU support (opsional)

## Cara Menggunakan

### 1. Start Ollama

```bash
docker-compose up -d
```

### 2. Cek Status Container

```bash
docker-compose ps
docker-compose logs -f ollama
```

### 3. Akses dari Host

Ollama API dapat diakses di:
- **API Endpoint**: `http://localhost:11434`
- **Contoh penggunaan**:
  ```bash
  curl http://localhost:11434/api/tags
  ```

### 4. Akses dari Container Lain

Container lain yang perlu mengakses Ollama harus terhubung ke network `ollama_network`:

```yaml
services:
  my-app:
    image: my-app:latest
    networks:
      - ollama_network

networks:
  ollama_network:
    external: true
```

Container lain dapat mengakses Ollama menggunakan hostname: `http://ollama:11434`

### 5. Stop Ollama

```bash
docker-compose down
```

Untuk menghapus volume juga:
```bash
docker-compose down -v
```

## GPU Support (NVIDIA)

Jika Anda memiliki NVIDIA GPU dan ingin menggunakannya:

1. Install [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
2. Uncomment bagian `deploy` di file `docker-compose.yml`
3. Restart container:
   ```bash
   docker-compose down
   docker-compose up -d
   ```

## Pull Model

Setelah container berjalan, Anda dapat pull model:

```bash
# Pull model llama2
curl http://localhost:11434/api/pull -d '{"name": "llama2"}'

# Atau menggunakan docker exec
docker exec ollama ollama pull llama2
```

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `OLLAMA_HOST` | `127.0.0.1` | Set ke `0.0.0.0` untuk akses dari luar container |
| `OLLAMA_ORIGIN` | `http://localhost:11434` | Mengatur origin yang diizinkan untuk request CORS |

## Troubleshooting

### Cek log container
```bash
docker-compose logs ollama
```

### Restart container
```bash
docker-compose restart ollama
```

### Rebuild container
```bash
docker-compose up -d --force-recreate
```
