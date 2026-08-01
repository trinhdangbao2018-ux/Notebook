Encode = tokenize → embedding → positional → transformer blocks → pooling → normalize

Bi-encode: Encode every chunk seperatedly, 
Exp:
text ("Why was 350 picked...")
  │ tokenize
  ▼
["why","was","350","pick","##ed",...]  → id: [2339,1108,8746,...]
  │ embedding lookup + positional encoding
  ▼
token vectors (mỗi token 1 vector nhỏ ban đầu)
  │ N lớp transformer (self-attention CHỈ trong nội bộ chính văn bản này —
  │ từ trong câu hỏi chỉ nhìn thấy từ khác TRONG câu hỏi, không thấy chunk nào cả)
  ▼
token vectors đã có ngữ cảnh (mỗi token giờ "biết" các từ xung quanh nó)
  │ pooling (gộp tất cả token-vector thành 1 vector duy nhất — mean hoặc lấy token [CLS])
  ▼
1 vector 384 chiều
  │ normalize (L2 — chia cho độ dài, thành vector đơn vị)
  ▼
vector đơn vị 384 chiều, ví dụ [0.12, -0.05, 0.33, ..., 0.02]

----------------------
cross-encode: Encode a lot of chunk. 
Exp:
"[CLS] Why was 350 picked... [SEP] Cutting Documents into Chunks... 350 tokens... [SEP]"
  │ tokenize (một chuỗi DUY NHẤT, câu hỏi + chunk dính liền nhau)
  ▼
token vectors (câu hỏi và chunk giờ là CÙNG MỘT chuỗi token)
  │ N lớp transformer (self-attention TRÊN TOÀN CHUỖI — mỗi token câu hỏi
  │ được phép "nhìn" trực tiếp từng token trong chunk, và ngược lại,
  │ ở MỌI lớp, không chỉ so sánh sau cùng)
  ▼
token vectors đã có ngữ cảnh CHÉO giữa câu hỏi và chunk
  │ lấy vector của token [CLS] (đại diện cho cả cặp)
  │ đưa qua 1 lớp phân loại nhỏ (linear layer)
  ▼
MỘT con số duy nhất (logit, ví dụ 4.77 hoặc -1.04 — không phải xác suất 0-1,
không phải vector để tái dùng)