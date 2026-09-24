# Knowledge Card Schema

## JSON Structure

```json
{
  "source_file": "document.pdf",
  "source_title": "Document Title",
  "card_count": 6,
  "cards": [
    {
      "title": "Card Title",
      "core_knowledge": "Core knowledge content.",
      "explanation": "Brief explanation.",
      "example": "Concrete example from the text.",
      "self_test": "Self-test question?",
      "summary": "Summary or deeper thinking."
    }
  ]
}
```

## Field Specifications

### Top-Level Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `source_file` | string | Yes | The filename of the source document |
| `source_title` | string | Yes | The title of the document (from content or filename) |
| `card_count` | integer | No | Number of cards (auto-calculated if omitted) |
| `cards` | array | Yes | Array of card objects (5-10 items, or fewer if source is limited) |

### Card Object Fields

| Field | Type | Required | Length | Description |
|-------|------|----------|--------|-------------|
| `title` | string | Yes | 5-15 words | Short, descriptive title naming the knowledge point |
| `core_knowledge` | string | Yes | 2-4 sentences | The essential fact or concept from the source |
| `explanation` | string | Yes | 2-4 sentences | Clarification of why or how the concept works |
| `example` | string | Yes | Variable | Concrete example from the source text, or "原文未提供示例" if none exists |
| `self_test` | string | Yes | 1-2 sentences | A question that tests understanding of this knowledge point |
| `summary` | string | Yes | 1-2 sentences | Summary of the key takeaway, or a deeper question/insight |

## Quality Rules

1. **One knowledge point per card** — never combine multiple concepts.
2. **No duplication** — each card must cover a unique knowledge point.
3. **No fabrication** — all content must come from the source document.
4. **No padding** — if the source only supports 3 cards, produce 3, not 5.
5. **No empty fields** — if a field has no source content, use the designated
   placeholder (e.g., "原文未提供示例" for the example field).

## Example Card

```json
{
  "title": "HTTP/2 Server Push Mechanism",
  "core_knowledge": "HTTP/2 Server Push allows a server to send resources to the client proactively before the client requests them. The server uses PUSH_PROMISE frames to notify the client of pending push operations.",
  "explanation": "Server Push reduces latency by eliminating the round-trip time for resource discovery. The client can cancel a push stream by sending a RST_STREAM frame if the pushed resource is already cached.",
  "example": "The article describes a server pushing CSS and JavaScript files alongside an HTML response, allowing the browser to render the page without waiting for additional requests.",
  "self_test": "How does a client cancel an unwanted server push in HTTP/2?",
  "summary": "Server Push is a powerful latency optimization but requires careful cache management to avoid pushing resources the client already has."
}
```
