# Kỹ thuật RAG Grounding Dập tắt Ảo giác Hợp đồng Thu mua    

***Phần 1: Phân tích nguyên nhân AI bị "ảo giác" (Hallucination):***
Hiện tượng nhân viên nhận được câu trả lời sai lệch nhưng cực kỳ tự tin từ ChatGPT xuất phát từ sự khác biệt cơ bản giữa Trí nhớ nội tại (Parametric Memory) và RAG Grounding:

Hỏi LLM không kèm tài liệu (Zero-context):

Mô hình ngôn ngữ lớn (LLM) không phải là một cơ sở dữ liệu tra cứu chính xác, mà là một cỗ máy dự đoán từ tiếp theo dựa trên xác suất thống kê từ kho dữ liệu khổng lồ trên Internet.

Khi nhân viên hỏi một câu hỏi mang tính đặc thù nội bộ ("Theo quy định MartX..."), AI hoàn toàn không có thông tin thực tế về hợp đồng của công ty. Tuy nhiên, do kiến trúc của AI được thiết kế để luôn đưa ra câu trả lời mượt mà, hợp lý và có vẻ thuyết phục, nó đã tự động "suy đoán" và tổng hợp các tiêu chuẩn phạt thông thường của ngành bán lẻ trên mạng để tạo ra một con số giả định (20% và đình chỉ 30 ngày).

Bẫy tự tin thái quá: AI không có cảm giác "nghi ngờ" hay "không biết" nếu không có dữ liệu ràng buộc. Nó trình bày thông tin sai sự thật với văn phong hành chính trang trọng, khiến người dùng lầm tưởng đó là quy định chính xác và gây ra tranh chấp pháp lý nghiêm trọng với đối tác.

***Phần 2: Cấu trúc RAG Prompt hoàn chỉnh***
Để triệt tiêu hoàn toàn khả năng bịa đặt của AI, chúng ta bắt buộc phải sử dụng kỹ thuật RAG (Retrieval-Augmented Generation) kết hợp với Negative Constraint (Khóa an toàn tiêu cực) theo cấu trúc 3 phần rõ ràng dưới đây:


***PHẦN 1: VĂN BẢN HỢP ĐỒNG CHUẨN - SOURCE MATERIAL***
Dưới đây là trích đoạn chính thức từ Hợp đồng hợp tác thương mại của MartX:
"Điều 8. Quy định về chất lượng hàng hóa và chế tài vi phạm:
- Trường hợp nhà cung cấp giao hàng nông sản, rau củ bị dập nát, hư hỏng: MartX áp dụng mức phạt 5% giá trị lô hàng bị dập nát và yêu cầu đối tác đổi trả hàng mới trong vòng 24h kể từ khi nhận được thông báo."

***PHẦN 2: CÂU HỎI NGHIỆP VỤ - USER QUERY***
Theo quy định MartX, nhà cung cấp giao rau bị dập nát trên 10% thì bị phạt bao nhiêu và có yêu cầu gì khác không?

***PHẦN 3: NEGATIVE CONSTRAINT - KHÓA AN TOÀN***
- Ràng buộc tuyệt đối: Bạn chỉ được phép sử dụng thông tin được cung cấp trực tiếp trong [PHẦN 1] để trả lời câu hỏi. 
- Nghiêm cấm tự suy đoán, ngoại suy hoặc sử dụng dữ liệu bên ngoài Internet.
- Nếu thông tin chi tiết (ví dụ: tỷ lệ cụ thể "trên 10%") không được đề cập rõ ràng trong văn bản nguồn, tuyệt đối phải trả lời: "Không có dữ liệu trong tài liệu". Không được tự bịa ra các con số chế tài hay thời gian đình chỉ.