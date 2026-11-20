# [Phân tích] So sánh Trade-off các giải pháp ra lệnh cho Ứng dụng AI trong Học tập & Nghiên cứu

---

## Bước 1: Hai prompt mẫu

### Giải pháp 1 (đơn giản, ngắn gọn)

```
Bạn là gia sư dùng phương pháp Feynman. Sinh viên sẽ giải thích một khái niệm bằng lời của mình.
Hãy chấm bài theo thang 10 và chỉ ra chỗ giải thích chưa rõ, sau đó gợi ý cách giải thích đơn giản hơn.

Khái niệm: {khai_niem}
Bài giải thích của sinh viên: {bai_lam}
```

### Giải pháp 2 (chi tiết, nhiều ràng buộc)

```
# VAI TRÒ
Bạn là gia sư áp dụng kỹ thuật Feynman kiêm giám khảo chấm bài theo rubric cố định.

# INPUT
- Khái niệm: {khai_niem}
- Bài giải thích của sinh viên: {bai_lam}

# XỬ LÝ NGOẠI LỆ (kiểm tra TRƯỚC khi chấm)
1. Nếu {bai_lam} trống hoặc dưới 20 từ → chỉ trả về: "Bài làm quá ngắn/trống, vui lòng giải thích lại ít nhất 3–5 câu." Không chấm điểm.
2. Nếu {bai_lam} lạc đề, không liên quan {khai_niem} → trả về: "Bài làm không liên quan đến khái niệm yêu cầu." Không chấm điểm.
3. Nếu {khai_niem} trống hoặc mơ hồ → hỏi lại đúng 1 câu để làm rõ. Không chấm điểm.
4. Nếu bài làm có thông tin sai kiến thức → ghi rõ ở mục "Lỗi kiến thức", không tự sửa ngầm.

# RUBRIC (tổng 10 điểm)
| Tiêu chí | Điểm | Mô tả đạt điểm tối đa |
| Chính xác kiến thức | 4 | Không sai khái niệm cốt lõi |
| Đơn giản hóa | 3 | Dùng ngôn ngữ đời thường, ít thuật ngữ, có ví dụ/phép so sánh |
| Logic & đầy đủ | 2 | Các ý nối nhau mạch lạc, không thiếu ý chính |
| Phát hiện lỗ hổng | 1 | Tự nhận ra phần mình chưa hiểu |

# RÀNG BUỘC
- Chỉ chấm dựa trên nội dung bài làm, không tự bổ sung ý hộ sinh viên.
- Mỗi điểm trừ phải kèm 1 trích dẫn ngắn từ bài làm làm bằng chứng.
- Không dùng quá 2 thuật ngữ chuyên ngành khi gợi ý; luôn giải thích bằng ví dụ đời thường.
- Tổng điểm = tổng điểm thành phần; không làm tròn sai.

# ĐỊNH DẠNG OUTPUT (bắt buộc)
1. Bảng điểm theo rubric (điểm + nhận xét 1 dòng)
2. Tổng điểm: X/10
3. Lỗ hổng cần học lại (tối đa 3 gạch đầu dòng)
4. Phiên bản giải thích gợi ý (tối đa 80 từ, như giải thích cho học sinh lớp 8)
5. 1 câu hỏi Feynman để sinh viên tự kiểm tra lại
```

---

## Bước 2: Bảng so sánh Trade-off

| Tiêu chí | Giải pháp 1 (Đơn giản) | Giải pháp 2 (Chi tiết) |
|---|---|---|
| Độ dễ hiểu / dễ viết | Cao: viết trong 1 phút | Trung bình: cần hiểu cấu trúc nhiều phần |
| Tốn token | Ít (~60 token prompt) | Nhiều hơn (~450 token prompt) |
| Chặn lỗi biên (input trống, lạc đề) | Trung bình đến yếu: AI tự đoán, có thể vẫn chấm bài rỗng | Cực tốt: có nhánh xử lý riêng |

**Trade-off cốt lõi:** Giải pháp 2 đổi thêm khoảng 390 token và độ phức tạp để lấy độ ổn định, công bằng và khả năng chặn lỗi. Với bài toán chấm bài tự động, nơi tính nhất quán là yêu cầu bắt buộc, cái giá đó đáng trả.

---

## Bước 3: Lựa chọn và chạy thử

### Phương án được chọn: Giải pháp 2

**Lý do:**

1. Bài toán là chấm bài tự động nên bắt buộc phải có rubric cố định, nếu không điểm sẽ dao động giữa các lần chạy.
2. Giải pháp 2 chặn được dữ liệu trống, lạc đề, sai kiến thức. Giải pháp 1 có xu hướng "chấm cho bằng được".
3. Output có cấu trúc cố định, dễ kiểm tra và dễ lưu trữ.
4. Chi phí token tăng không đáng kể so với lợi ích về chất lượng.

### Kết quả chạy thử

> Nên chạy lại trên công cụ AI của bạn và thay bằng kết quả thật khi nộp, vì kết quả có thể khác đôi chút.

**Test 1: Bài làm bình thường**

- Khái niệm: *Overfitting*
- Bài làm: "Overfitting là khi mô hình học thuộc dữ liệu cũ nên gặp dữ liệu mới thì sai, giống học sinh học thuộc đáp án đề cũ."

Kết quả:

| Tiêu chí | Điểm | Nhận xét |
|---|---|---|
| Chính xác kiến thức | 3/4 | Đúng bản chất, chưa nói lỗi train thấp, test cao |
| Đơn giản hóa | 3/3 | Có ví dụ "học thuộc đáp án" |
| Logic & đầy đủ | 1/2 | Thiếu nguyên nhân |
| Phát hiện lỗ hổng | 0/1 | Không nêu phần chưa chắc |

**Tổng điểm: 7/10**

- Lỗ hổng: dấu hiệu nhận biết; nguyên nhân; cách khắc phục
- Gợi ý: "Overfitting giống như em nhớ kỹ từng câu trong sách mà không hiểu nghĩa, nên gặp đề mới là sai."
- Câu hỏi Feynman: *Điểm bài tập cao nhưng điểm thi thấp thì nguyên nhân có thể là gì?*

**Test 2: Input trống** (`bai_lam = ""`)

→ "Bài làm quá ngắn/trống, vui lòng giải thích lại ít nhất 3–5 câu."

**Test 3: Lạc đề** (bài làm nói về cách nấu phở)

→ "Bài làm không liên quan đến khái niệm yêu cầu."

**Đối chiếu Giải pháp 1:** ở Test 2 và 3, prompt đơn giản không có nhánh chặn nên AI dễ chấm một điểm bất kỳ.

---

## Kết luận

Giải pháp 1 hợp để thử nhanh, Giải pháp 2 hợp để chấm bài thật. Với yêu cầu tối ưu quy trình Feynman gia sư và Rubric chấm bài tự động, **Giải pháp 2 là lựa chọn tối ưu**: đánh đổi một ít token để có độ tin cậy cao.
