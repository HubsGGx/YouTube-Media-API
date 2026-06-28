# YouTube Media API - Documentação Oficial

Este documento detalha a **YouTube Media API**, um sistema robusto para a extração e conversão de conteúdo de mídia do YouTube. A API suporta a obtenção de áudio (MP3) e vídeo (MP4), fornecendo links temporários para streaming e acesso a metadados detalhados. Desenvolvida para desenvolvedores, a API integra funcionalidades de gerenciamento de cache e expiração automática de links, otimizando a eficiência e o uso de recursos.

---

## Informações Gerais

A API opera sob as seguintes especificações técnicas:

*   **Base URL:** `https://fluxdevservice/api/download`
*   **Formato de Dados:** `JSON`
*   **Autenticação:** Requer uma chave de API válida, fornecida via parâmetro `key`.
*   **Tempo de Vida do Link:** Os links gerados possuem validade de 2 horas.
*   **Timeout Padrão:** O tempo limite para streaming é de 30 segundos, enquanto o processamento pode variar.

---

## Endpoints da API

### 1. Conversão para MP3 (Áudio)

Este endpoint facilita a conversão de um vídeo do YouTube para o formato MP3, retornando um identificador único (`id`) para acesso ao stream de áudio.

**Endpoint:** `GET /ytmp3?key={KEY}&q={QUERY}`

| Parâmetro | Tipo   | Obrigatório | Descrição                                        |
| :-------- | :----- | :---------- | :----------------------------------------------- |
| `key`     | string | Sim         | Chave de API válida para autenticação.           |
| `q`       | string | Sim         | Termo de busca (nome do vídeo, palavra-chave ou URL do YouTube). |

#### Resposta de Sucesso (HTTP 200 OK)

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

### 2. Conversão para MP4 (Vídeo)

Este endpoint converte um vídeo do YouTube para o formato MP4 (vídeo com áudio), fornecendo um `id` para acesso ao stream de vídeo.

**Endpoint:** `GET /ytmp4?key={KEY}&q={QUERY}`

| Parâmetro | Tipo   | Obrigatório | Descrição                                        |
| :-------- | :----- | :---------- | :----------------------------------------------- |
| `key`     | string | Sim         | Chave de API válida para autenticação.           |
| `q`       | string | Sim         | Termo de busca (nome do vídeo, palavra-chave ou URL do YouTube). |

#### Resposta de Sucesso (HTTP 200 OK)

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

### 3. Obtenção de Arquivo de Mídia (Streaming)

Este endpoint é utilizado para o streaming direto do arquivo de mídia (MP3/MP4). É ideal para integração com elementos `<audio>` ou `<video>` em HTML5.

**Endpoint:** `GET /youtube/media?id={ID}`

> **Nota:** A autenticação via chave de API não é necessária para este endpoint, pois o `id` já incorpora a validade e a autorização temporária.

| Parâmetro | Tipo   | Obrigatório | Descrição                                        |
| :-------- | :----- | :---------- | :----------------------------------------------- |
| `id`      | string | Sim         | Identificador único da mídia, obtido nos endpoints de conversão. |

**Headers de Resposta:**

*   `Content-Type`: `audio/mpeg` ou `video/mp4`
*   `Content-Disposition`: `inline; filename="titulo_do_video.mp3"` (pode ser alterado para `attachment` para forçar o download).

---

### 4. Obtenção de Informações da Mídia (Metadados)

Este endpoint retorna metadados completos de uma mídia que foi previamente processada, incluindo título, duração e thumbnail.

**Endpoint:** `GET /youtube/info?id={ID}`

| Parâmetro | Tipo   | Obrigatório | Descrição                                        |
| :-------- | :----- | :---------- | :----------------------------------------------- |
| `id`      | string | Sim         | Identificador único da mídia.                    |

#### Resposta de Sucesso (HTTP 200 OK)

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

## Tratamento de Erros

A API utiliza códigos de status HTTP e mensagens de erro padronizadas para indicar problemas nas requisições:

| HTTP Status | Código do Erro         | Descrição                                                              |
| :---------- | :--------------------- | :--------------------------------------------------------------------- |
| `400`       | `INVALID_QUERY`        | O parâmetro `q` está ausente ou é inválido.                           |
| `401`       | `INVALID_KEY`          | A chave de API fornecida é inválida ou está ausente.                   |
| `404`       | `VIDEO_NOT_FOUND`      | Nenhum vídeo foi encontrado para o termo de busca especificado.       |
| `404`       | `MEDIA_NOT_FOUND`      | A mídia solicitada expirou ou não foi encontrada.                      |
| `500`       | `PROCESSING_ERROR`     | Ocorreu uma falha interna durante o processamento da mídia.            |
| `504`       | `PROCESSING_TIMEOUT`   | O tempo limite para o processamento da mídia foi excedido.             |

---

## Regras de Negócio e Considerações Finais

*   **Validade dos IDs:** Todos os identificadores de mídia (`id`) gerados pela API expiram automaticamente após 2 horas.
*   **Limpeza de Cache:** O sistema implementa uma rotina de limpeza automática a cada 30 minutos para remover entradas de cache expiradas.
*   **Mecanismo de Busca:** A API emprega algoritmos avançados para identificar o vídeo mais relevante no YouTube com base no parâmetro `q` fornecido.
*   **Streaming vs. Download:** A API disponibiliza links para streaming de mídia. Para iniciar um download direto, o cliente deve configurar adequadamente o cabeçalho `Content-Disposition`.
*   **Disponibilidade de Vídeos:** É fundamental assegurar que o vídeo do YouTube alvo seja público e não esteja sujeito a restrições de idade ou geográficas.

---

Desenvolvido por [hubsqwGG](https://github.com/hubsqwGG).
