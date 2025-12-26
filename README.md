# Multimodal Question Answering on Traffic Sign  
*Tất cả code được thực hiện ở Google Colab (GPU T4)*

## Giới thiệu

Dự án Multimodal Question Answering on Traffic Sign xây dựng một hệ thống hỏi-đáp đa phương thức (MQA) cho bài toán hiểu và trả lời câu hỏi pháp lý liên quan đến biển báo giao thông Việt Nam.

Hệ thống kết hợp hình ảnh giao thông (biển báo, bối cảnh đường phố) và văn bản (câu hỏi, lựa chọn trả lời, điều luật) để:
- Truy hồi các điều luật liên quan
- Trả lời câu hỏi trắc nghiệm hoặc Đúng/Sai

Dự án được thiết kế theo hướng training-free, không huấn luyện mô hình mới, mà khai thác các mô hình thị giác-ngôn ngữ và kỹ thuật truy hồi vector.

## Mục tiêu

- Xây dựng hệ thống Multimodal Legal Question Answering cho biển báo giao thông Việt Nam  
- Kết hợp Computer Vision, NLP và Vector Search trong một pipeline thống nhất  
- Thiết kế pipeline training-free, mô-đun, dễ mở rộng  
- Giải quyết hai nhiệm vụ:
  - Subtask 1 - Truy hồi điều luật  
    - Đánh giá bằng F2-score (ưu tiên Recall)
  - Subtask 2 - Trả lời câu hỏi  
    - Đánh giá bằng Accuracy


## Tổng quan hệ thống

Hệ thống hoạt động theo pipeline nhiều giai đoạn, gồm các bước chính:

1. Biểu diễn câu hỏi bằng text embedding  
2. Phát hiện biển báo trong ảnh giao thông  
3. Lọc các biển báo liên quan đến câu hỏi  
4. Truy hồi điều luật bằng vector search  
5. Trả lời câu hỏi bằng Vision-Language Model

Thiết kế này giúp cân bằng giữa độ chính xác và khả năng bao phủ điều luật.


## Dữ liệu

- Bộ câu hỏi - hình ảnh: MLQA-TSR  
- Cơ sở dữ liệu luật:
  - QCVN 41:2024/BGTVT - Quy chuẩn báo hiệu đường bộ
  - Luật Trật tự, An toàn Giao thông Đường bộ 36/2024/QH15

## Biểu diễn đa phương thức

Hệ thống sử dụng ba loại embedding:

- Text embedding: câu hỏi và các lựa chọn trả lời  
- Image embedding: toàn bộ ảnh giao thông  
- Object-level embedding: từng biển báo được phát hiện trong ảnh  

Toàn bộ embedding được lưu trữ và truy vấn bằng Vector Database (Qdrant).


## Pipeline truy hồi điều luật (Subtask 1)

Truy hồi điều luật được thực hiện theo pipeline ba giai đoạn:

### Giai đoạn 1 - Lọc theo văn bản
- Text embedding + vector search  
- Truy hồi top-N mẫu có ngữ cảnh pháp lý gần nhất  

### Giai đoạn 2 - Lọc theo hình ảnh
- Image embedding + vector search  
- Lọc xuống top-M mẫu có bối cảnh thị giác tương đồng  

### Giai đoạn 3 - Xếp hạng theo biển báo
- Object-level embedding cho từng biển báo  
- Xếp hạng bằng MaxSim similarity  
- Chọn top-K mẫu phù hợp nhất  

Hậu xử lý:
- Gộp điều luật trùng lặp
- Mỗi điều luật chỉ xuất hiện một lần
- Tối ưu theo F2-score, ưu tiên Recall


## Pipeline trả lời câu hỏi (Subtask 2)

- Sử dụng kết quả truy hồi của Subtask 1 làm ngữ cảnh và ví dụ few-shot
- Prompt đầu vào cho Vision-Language Model gồm:
  - Ảnh giao thông
  - Câu hỏi
  - Các lựa chọn trả lời
  - Mô tả biển báo
  - Điều luật liên quan
- Mô hình sinh câu trả lời cuối cùng:
  - Chọn A/B/C/D hoặc Đúng/Sai


## Công nghệ sử dụng

- Traffic sign detection: YOLO, GroundingDINO  
- Text embedding: Jina Embeddings  
- Image embedding: CLIP  
- Object-level embedding: CLIP  
- Vector database: Qdrant  
- Vision-Language Model: Gemma-3  
- Programming language: Python  
- Deep learning framework: PyTorch  


## Đánh giá

- Subtask 1: F2-score (ưu tiên Recall để hạn chế bỏ sót điều luật)  
- Subtask 2: Accuracy (đánh giá trực tiếp câu trả lời)


## Ghi chú

Dự án mang tính nghiên cứu và học thuật, phù hợp cho:
- Khóa luận tốt nghiệp
- Nghiên cứu Multimodal Question Answering
- Legal AI
- Hệ thống hỗ trợ giao thông thông minh
