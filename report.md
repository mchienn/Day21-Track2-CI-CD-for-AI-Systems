# Báo cáo MLOps Lab — CI/CD cho AI Systems

## Kết quả thí nghiệm (Bước 1)

Chạy 5 thí nghiệm với RandomForestClassifier trên tập Wine Quality (2998 mẫu huấn luyện, 500 mẫu đánh giá).

| Lần | n_estimators | max_depth | min_samples_split | Accuracy | F1 Score |
|-----|-------------|-----------|-------------------|----------|----------|
| 1 | 100 | 5 | 2 | 0.5640 | 0.5534 |
| 2 | 50 | 3 | 2 | 0.5580 | 0.5185 |
| 3 | 200 | 10 | 5 | 0.6440 | 0.6417 |
| 4 | 200 | 15 | 2 | 0.6640 | 0.6620 |
| 5 | 200 | null | 2 | **0.6740** | **0.6730** |

**Bộ siêu tham số tốt nhất:** `n_estimators=200, max_depth=null, min_samples_split=2` (Accuracy: 0.6740, F1: 0.6730). max_depth=null cho phép cây phát triển tự do, tận dụng tối đa dữ liệu.

## CI/CD Pipeline (Bước 2)

Pipeline GitHub Actions gồm 4 jobs: Unit Test → Train → Eval Gate → Deploy. Eval gate chặn deploy nếu accuracy < 0.70.

## Huấn luyện liên tục (Bước 3)

Khi dữ liệu mới được thêm (train_phase2: 2998 mẫu), DVC cập nhật con trỏ và git push kích hoạt pipeline tự động.

## Khó khăn gặp phải

1. Xung đột phiên bản thư viện với Python 3.13 — scikit-learn 1.4.2 không có wheel cho Python 3.13, phải nâng cấp lên phiên bản mới hơn.
2. MLflow API thay đổi: tham số `artifact_path` bị deprecated, thay bằng `name`.
3. DVC cần push data trước git push để CI runner có thể pull dữ liệu mới.
