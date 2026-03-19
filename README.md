# demo_microservice_code

Kho chứa **mã nguồn ứng dụng** cho demo microservice Online Boutique.

---

## Cấu trúc dự án (Repo Code)

- `src/`  
  - Mã nguồn các microservice (frontend, cartservice, checkoutservice, v.v.).
- `protos/`  
  - Định nghĩa protobuf dùng để giao tiếp giữa các service.
- `docs/`  
  - Tài liệu kỹ thuật, ghi chú phát triển.
- `huong-dan-anh/`  
  - **Thư mục trống để bạn chèn ảnh hướng dẫn** (screenshot IDE, pipeline, log, v.v.).  
  - Có thể tham chiếu ảnh trong phần hướng dẫn triển khai bên dưới.

Repo DevOps/manifests nằm tại: `demo_microservice_devops` (GitHub: `lovelybugf/demo_microservice_manifest`).

---

## Hướng dẫn triển khai (Repo Code)

### 1. Quy trình phát triển và build image

1. **Phát triển / sửa code**
   - Làm việc trên nhánh feature hoặc trực tiếp trên `main` (tùy quy ước team).
2. **Chạy test/unit test cục bộ** (tùy ngôn ngữ/service).
3. **Build Docker image** (ví dụ):
   ```bash
   docker build -t <registry>/<project>/onlineboutique-<service>:<tag> .
   ```
4. **Push image lên registry**:
   ```bash
   docker push <registry>/<project>/onlineboutique-<service>:<tag>
   ```

> _Ghi chú_: Thông tin cụ thể về registry (Docker Hub, GHCR, Harbor, v.v.) nên được mô tả thêm ở đây theo môi trường thực tế của bạn.

### 2. Cập nhật repo manifests (GitOps)

Sau khi image mới đã được push:

1. Từ CI/CD hoặc thủ công, cập nhật repo manifests `demo_microservice_devops`:
   - Chỉnh sửa tag/digest của image trong Kustomize/Helm tương ứng.
   - Thường update vào nhánh `env/dev` trước (dev environment).
2. Argo CD sẽ theo dõi repo manifests và triển khai bản mới lên cluster.

Chi tiết quy trình GitOps xem trong README của repo `demo_microservice_devops`.

### 3. Nơi chèn ảnh hướng dẫn

- Đặt ảnh vào thư mục `huong-dan-anh/`, ví dụ:
  - `huong-dan-anh/build-image.png`
  - `huong-dan-anh/pipeline-success.png`
- Sau đó có thể nhúng vào README hoặc tài liệu trong `docs/`:
  ```markdown
  ![Build image thành công](huong-dan-anh/build-image.png)
  ```

---

## Liên kết với repo manifests

- Repo code này **không chứa Kubernetes manifests**.
- Tất cả manifests, RBAC, Argo CD, Kustomize… nằm trong repo:
  - `demo_microservice_devops` / GitHub: `lovelybugf/demo_microservice_manifest`
- Quy trình chuẩn:
  1. Sửa code → build/push image.
  2. Cập nhật repo manifests (tag/digest + cấu hình).
  3. Argo CD sync state từ repo manifests ra cụm Kubernetes.

