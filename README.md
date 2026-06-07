# 🎥 YouTube Media API - Documentação Oficial

> Sistema de alta performance para extração e conversão de mídia do YouTube (MP3/MP4), oferecendo links temporários de streaming e metadados detalhados.

A **YouTube Media API** permite que desenvolvedores integrem capacidades de download e streaming de conteúdo do YouTube em suas aplicações de forma simplificada. Com gerenciamento automático de cache e expiração de links, a API garante eficiência e economia de recursos.

---

## 🚀 Informações Gerais

*   **Base URL:** `https://hubsdev.com/api/download`
*   **Formato de Dados:** `JSON`
*   **Autenticação:** Chave de API via parâmetro `key`
*   **Tempo de Vida do Link:** 2 horas
*   **Timeout Padrão:** 30 segundos (streaming) / processamento variável

---

## 🛠 Endpoints Principais

### 1. YouTube para MP3 (Áudio)
Converte um vídeo do YouTube para o formato MP3 e retorna um identificador único para acesso ao stream.

**Endpoint:** `GET /ytmp3?key={KEY}&q={QUERY}`

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `key` | string | Sim | Sua chave de API válida. |
| `q` | string | Sim | Nome do vídeo, palavra-chave ou URL direta do YouTube. |

#### Resposta de Sucesso (200 OK)
```json
{
  "success": true,
  "message": "Áudio processado com sucesso.",
  "statusCode": 200,
  "data": {
    "id": "a1b2c3d4e5f6...",
    "type": "audio",
    "format": "mp3",
    "url": "/api/download/youtube/media?id=a1b2c3...",
    "infoUrl": "/api/download/youtube/info?id=a1b2c3...",
    "timestamp": "2026-06-06T14:30:00.000Z",
    "expires_at": "2026-06-06T16:30:00.000Z",
    "cost_charged": 0.05,
    "balance_after": "9.95"
  }
}
```

---

### 2. YouTube para MP4 (Vídeo)
Converte um vídeo do YouTube para o formato MP4 (vídeo com áudio) e retorna um ID para acesso ao stream.

**Endpoint:** `GET /ytmp4?key={KEY}&q={QUERY}`

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `key` | string | Sim | Sua chave de API válida. |
| `q` | string | Sim | Nome do vídeo, palavra-chave ou URL direta do YouTube. |

#### Resposta de Sucesso (200 OK)
```json
{
  "success": true,
  "message": "Vídeo processado com sucesso.",
  "statusCode": 200,
  "data": {
    "id": "f6e5d4c3b2a1...",
    "type": "video",
    "format": "mp4",
    "url": "/api/download/youtube/media?id=f6e5d4...",
    "infoUrl": "/api/download/youtube/info?id=f6e5d4...",
    "timestamp": "2026-06-06T14:30:00.000Z",
    "expires_at": "2026-06-06T16:30:00.000Z",
    "cost_charged": 0.05,
    "balance_after": "9.90"
  }
}
```

---

### 3. Obter Arquivo de Mídia (Streaming)
Endpoint dedicado que retorna o arquivo binário (MP3/MP4). Ideal para ser consumido diretamente por elementos `<audio>` ou `<video>` HTML5.

**Endpoint:** `GET /youtube/media?id={ID}`

> **Nota:** Este endpoint não exige chave de API, pois o ID já é temporário e autenticado indiretamente.

| Parâmetro | Tipo | Obrigatório | Descrição |
| :--- | :--- | :--- | :--- |
| `id` | string | Sim | ID da mídia obtido nos endpoints de conversão. |

**Headers de Resposta:**
*   `Content-Type`: `audio/mpeg` ou `video/mp4`
*   `Content-Disposition`: `inline; filename="titulo.mp3"`

---

### 4. Obter Informações da Mídia (Metadados)
Retorna os metadados completos de uma mídia previamente processada.

**Endpoint:** `GET /youtube/info?id={ID}`

#### Resposta de Sucesso (200 OK)
```json
{
  "success": true,
  "message": "Informações da mídia recuperadas com sucesso.",
  "statusCode": 200,
  "data": {
    "id": "a1b2c3d4...",
    "title": "Imagine - John Lennon",
    "duration": "3:05",
    "thumbnail": "https://i.ytimg.com/vi/.../hqdefault.jpg",
    "videoId": "dQw4w9WgXcQ",
    "mimeType": "audio/mpeg",
    "createdAt": "2026-06-06T14:30:00.000Z",
    "expiresAt": "2026-06-06T16:30:00.000Z",
    "available": true
  }
}
```

---

## ⚠️ Tratamento de Erros

| HTTP | Código do Erro | Descrição |
| :--- | :--- | :--- |
| 400 | `INVALID_QUERY` | O parâmetro `q` não foi informado ou é inválido. |
| 401 | `INVALID_KEY` | Chave de API não fornecida ou inválida. |
| 404 | `VIDEO_NOT_FOUND` | Nenhum vídeo encontrado para o termo pesquisado. |
| 404 | `MEDIA_NOT_FOUND` | Mídia expirada ou não encontrada (após 2h). |
| 500 | `PROCESSING_ERROR` | Falha interna ao processar a mídia. |
| 504 | `PROCESSING_TIMEOUT` | Tempo limite de processamento excedido. |

---

## ⚖️ Regras de Negócio e Notas

*   **Validade dos Links:** Todos os IDs gerados expiram automaticamente após **2 horas**.
*   **Limpeza de Cache:** O sistema realiza uma limpeza automática a cada 30 minutos para remover arquivos expirados.
*   **Motor de Busca:** A API utiliza algoritmos avançados para encontrar o vídeo mais relevante com base no termo fornecido.
*   **Streaming vs Download:** A API fornece links de streaming. Para forçar o download, o cliente deve tratar o header `Content-Disposition`.
*   **Disponibilidade:** Certifique-se de que o vídeo do YouTube é público e não possui restrições de idade ou região.

---

Desenvolvido por [hubsqwGG](https://github.com/hubsqwGG).
