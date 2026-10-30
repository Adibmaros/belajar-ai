Peta Belajar AI & RAG

Urutannya dari fondasi ke atas. Tiap tahap saya kaitkan ke file di proyek ini supaya Anda bisa membedah kode yang sudah ada.

Tahap 0: Fondasi (Prasyarat)

## Materi Dasar

| Yang dipelajari | Kenapa penting | Latihan |
| :---- | :---- | :---- |
| **Python dasar** (list, dict, function, class, venv, pip) | Fondasi utama scripting AI & data pipeline | Buat skrip pemroses data sederhana |
| **TypeScript/JS async** (Promise, async/await, fetch) | Penanganan request ke API LLM & backend asynchronous | Integrasi API external dengan async/await |
| **SQL** (SELECT, JOIN, GROUP BY, index) | Pengelolaan basis data relasional & vector DB | Kueri agregasi & optimasi index database |
| **HTTP & REST** (method, status code, header) | Komunikasi antar-layanan & penyediaan endpoint API | Buat REST API sederhana |
| **Linear algebra ringan** (vektor, dot product, norm) | Memahami perhitungan jarak & kemiripan embedding | Hitung cosine similarity secara manual |

Tahap 1: Konsep Inti ML & LLM

1. **Apa itu model, parameter, training vs inference.**  
2. **Tokenization.** Teks dipecah jadi token. Cobalah tokenizer visual online.  
3. **Transformer & attention** (level konseptual). Cukup paham ide "kata memperhatikan kata lain".  
4. **Context window.** Alasan chunking dan batas max\_tokens ada.  
5. **Temperature, top\_p.** Lihat temperature: 0.3 di kode Anda, lalu coba ubah ke 0 dan 1\.

**Hallucination & grounding.** Alasan prompt Anda berbunyi "jangan mengarang".

Sumber: seri "Neural Networks" (3Blue1Brown), "Intro to LLMs" (Andrej Karpathy), dan Karpathy "Let's build GPT" untuk yang ingin dalam.

Tahap 2: Embedding & Vector Search (Jantung RAG)

| Konsep | File untuk dibedah | Eksperimen |
| :---- | :---- | :---- |
| Embedding (teks → vektor) | [embedding.ts](file:///c:/Users/user/Desktop/portfolio-rebuild/server/utils/ai/embedding.ts) | Cetak vektor dua kalimat mirip dan dua kalimat beda, bandingkan angkanya |
| pooling: 'mean', normalize: true | embedding.ts | Matikan normalize, lihat apa yang berubah pada similarity |
| Dimensi (384) | setup-db-tables.mjs | Cari tahu kenapa tabel harus VECTOR(384) |
| Cosine distance \<=\> | fungsi match\_portfolio\_content | Ganti ke \<-\> (L2) atau \<\#\> (inner product), bandingkan hasilnya |
| Index HNSW vs IVFFlat | setup-db-tables.mjs | Baca perbedaannya, jalankan EXPLAIN ANALYZE |
| Threshold (0.15) | ask.post.ts | Naikkan ke 0.5, amati dokumen mana yang hilang |
| Model embedding lain | [Xenova/transformers](https://huggingface.co/Xenova) | Ganti ke model multilingual, lihat skor kueri bahasa Indonesia |

Tahap 3: Ingestion Pipeline

1. **Parsing dokumen** (pdf-parse). Coba PDF hasil scan lalu lihat kenapa kosong. Pelajari OCR (Tesseract).  
2. **Cleaning teks** (regex di extractTextFromPdf).  
3. **Chunking.** Ini area eksperimen terbaik:  
   1. Fixed-size vs paragraph vs semantic chunking  
   2. Ukuran chunk dan **overlap**  
   3. Chunk yang membawa judul bagian (*contextual chunking*)  
4. **Metadata design.** Perhatikan field documentKey, topic, chunk di indexKnowledgePdf. Metadata ini memungkinkan filtering.

**Idempotency.** Alasan deleteKnowledgePdf dipanggil sebelum insert ulang.

Tahap 4: Retrieval (Titik Kritis)

Ini tahap yang tadi bermasalah, jadi paling layak dipelajari detail:

1. **Dense retrieval** (vektor) dan **sparse retrieval** (kata kunci, BM25).  
2. **Hybrid search.** Bedah gabungan knowledgeIntentMatches \+ keywordMatches \+ rawMatches di ask.post.ts.  
3. **PostgreSQL full-text search** (tsvector, to\_tsquery) sebagai pengganti ILIKE.  
4. **Reranking** (cross-encoder, Reciprocal Rank Fusion).  
5. **Query rewriting & expansion.**  
6. **Metadata filtering.**

**Evaluasi retrieval.** Metrik: hit rate, MRR, recall@k. Buat 20 pertanyaan uji beserta dokumen jawaban yang benar, lalu ukur.

Tahap 5: Generation (LLM & Prompting)

| Topik | Di kode Anda | Eksperimen |
| :---- | :---- | :---- |
| Prompt engineering | systemPrompt | Ubah aturan \#4 (anti-halusinasi), lalu coba pancing halusinasi |
| Conversation memory | body.history.slice(-4) | Naikkan jadi 10, lihat dampak token dan biaya |
| Rate limit, retry, fallback | loop CANDIDATE\_MODELS | Tambahkan exponential backoff untuk 429 |
| Streaming (SSE) | belum ada | Implementasikan streaming supaya jawaban muncul bertahap |
| Structured output / JSON mode | belum ada | Minta LLM mengembalikan JSON sumber referensi |
| Token & biaya | max\_tokens: 1500 | Hitung estimasi biaya per request |

Tahap 6: Evaluasi & Observability

1. Logging query, dokumen terambil, skor, dan jawaban. Tabel service\_health\_logs bisa jadi pola.  
2. **Golden dataset**: pasangan pertanyaan dan jawaban ideal.  
3. Metrik generation: faithfulness (setia ke konteks), answer relevancy.

Tool: **Ragas, TruLens, LangSmith, Langfuse**.

Tahap 7: Level Lanjut (Setelah RAG Paham)

* **Function calling / tool use / agent.** LLM memanggil fungsi, misalnya "cek status invoice".  
* **MCP (Model Context Protocol).**  
* **Fine-tuning vs RAG.** Kapan memakai yang mana.  
* **Multimodal** (gambar, PDF scan via vision model).  
* **Guardrails & keamanan.** Prompt injection, PII, rate limit di endpoint publik /api/ai/ask.

**Caching** semantik atau per-kueri untuk menekan biaya.

Tools & Bahasa Ringkas

| Kategori | Yang Anda pakai sekarang | Yang layak dikenal |
| :---- | :---- | :---- |
| Bahasa | TypeScript | **Python** (wajib dikenal) |
| Embedding | @xenova/transformers | sentence-transformers, OpenAI/Cohere/Voyage embeddings |
| Vector DB | pgvector (Supabase) | Qdrant, Pinecone, Chroma, Weaviate |
| LLM API | Groq | OpenAI, Anthropic, Google Gemini, Ollama (lokal) |
| Orkestrasi | tulis manual | LangChain, LlamaIndex (pahami dulu versi manualnya, seperti yang sudah Anda punya) |
| Parsing | pdf-parse | unstructured, docling, pymupdf |
| Evaluasi | belum ada | Ragas, Langfuse |

Urutan Latihan yang Disarankan

1. **Minggu 1:** Buat skrip kecil yang mencetak embedding dan cosine similarity dua kalimat. Pahami generateEmbedding sampai ke angka.  
2. **Minggu 2:** Eksperimen chunking (ukuran 300/800/1500, overlap 0/100). Ukur dampaknya ke skor di sandbox admin.  
3. **Minggu 3:** Buat golden dataset 20 pertanyaan, lalu ukur hit rate sebelum dan sesudah perubahan retrieval.  
4. **Minggu 4:** Ganti ILIKE dengan full-text search, tambah reranking RRF, lalu bandingkan metriknya.  
5. **Minggu 5:** Tulis ulang pipeline sederhana ini di Python (sentence-transformers \+ pgvector) agar paham konsep terlepas dari bahasanya.

**Setelahnya:** Streaming, function calling, dan guardrails.

\*\*Tips belajarnya:\*\* ubah satu variabel, ukur dampaknya, lalu catat. Dengan begitu Anda paham \*kenapa\* kode bekerja, bukan hanya \*bahwa\* kode bekerja. Kalau mau, saya bisa buatkan skrip eksperimen (misalnya skrip evaluasi retrieval dengan golden dataset) di folder \`scripts/\` sebagai bahan latihan minggu 1–3.  
