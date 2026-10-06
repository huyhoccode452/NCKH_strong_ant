# Đức cần sửa lại — review nhánh `duc/track_b/xuanphuong` (commit `1123a29`)

> Đây là vòng review thứ 2, sau khi Đức đã push commit
> `1123a29 "Update track_b Xuan Phuong data and reports"`. Tài liệu này chỉ rõ
> **2 lỗi cụ thể** (Lỗi 2 và Lỗi 3 bên dưới) kèm hướng dẫn sửa từng bước.
>
> **ĐÍNH CHÍNH (06/10/2026):** "Lỗi 1" trong bản đầu của tài liệu này (nghi Đức chưa
> chạy lại B5) là **kết luận sai của reviewer** — đã được xác minh lại, xem mục dưới.
> Đức đã sửa xong Lỗi 2 và Lỗi 3 trong commit `457b342`.

## ~~Lỗi 1~~ — ĐÃ XÁC MINH: KHÔNG PHẢI LỖI (reviewer kết luận sai)

Bản đầu của tài liệu này nghi Đức chưa chạy lại B5, vì `total_population_across_cells`
vẫn là `108031.635` sau khi đổi tên file raster. Suy luận đó **sai**: reviewer đã
bỏ sót khả năng file `vnm_ppp_2020_100m.tif` cũ thực chất là **cùng một file**
với `vnm_ppp_2020_UNadj_constrained.tif`, chỉ khác tên và thư mục.

Đã xác minh độc lập ngày 06/10/2026:
- SHA256 raster Đức công bố (`02f02659…252ef0`, 17.612.362 bytes) **khớp bit-for-bit**
  với file chuẩn của leader.
- Tái tạo từ đầu (lưới H3 từ `xuan_phuong_boundary.geojson` + zonal sum trên raster
  chuẩn) ra đúng 15 ô (1 res 7 + 14 res 8), tổng `108031.635`, min/max
  `143.07 / 45005.18` — **trùng 100%** với số liệu trong tài liệu của Đức.

Kết luận: số liệu dân số Xuân Phương đúng. Đức không cần chạy lại B5/B3.
Việc Đức làm đúng (đối chiếu SHA256 với nguồn WorldPop và ghi vào tài liệu) là
cách xác minh chuẩn — nên giữ làm mẫu cho các phường sau.

## Lỗi 2: Đã xóa dòng `.gitignore` chặn file bản đồ lớn, khiến PBF bị commit thẳng vào Git

**Bằng chứng:** commit `1123a29` xóa hẳn đoạn sau khỏi `.gitignore`:

```gitignore
maps/*.pbf
maps/01.json
maps/gadm41_VNM_1.json
```

Hậu quả: `maps/hanoi.osm.pbf` (**21.8 MB**), `maps/chuong_duong.osm.pbf`,
`maps/xuan_phuong.osm.pbf` đã bị commit thẳng vào lịch sử Git. Đây là dữ liệu
lớn, tải lại được từ Geofabrik/cắt lại được bằng `osmium` — không được phép
nằm trong Git (nguyên tắc `docs/context.md` mục 8: "không commit dữ liệu lớn
lên git"). Nếu nhánh này được merge nguyên trạng, mọi người sau này clone repo
sẽ phải tải thêm hơn 20MB không cần thiết, và sẽ tăng dần theo mỗi lần có
người khác lặp lại lỗi tương tự.

**Cách sửa:**

1. Khôi phục lại đúng 3 dòng đã xóa vào `.gitignore` (copy nguyên văn từ trên).
2. Gỡ các file PBF khỏi Git nhưng **giữ nguyên trên đĩa** (không xóa file, chỉ
   ngừng theo dõi):
   ```powershell
   git rm --cached maps/hanoi.osm.pbf maps/chuong_duong.osm.pbf maps/xuan_phuong.osm.pbf
   ```
3. Commit lại việc gỡ này kèm `.gitignore` đã khôi phục.

**Bài học:** khi gặp lỗi "file bị chặn không add được", việc cần làm là kiểm
tra xem file đó CÓ NÊN bị chặn không (thường là có lý do), chứ không phải xóa
luôn dòng chặn trong `.gitignore` cho hết vướng.

## Lỗi 3: Đã xóa `ingest_era5_weather.py` — file này vẫn đang được dùng chung

**Bằng chứng:** commit `1123a29` xóa toàn bộ 145 dòng của
`ingest_era5_weather.py`.

File này không phải code thừa — nó vẫn được nhắc trong
`docs/chuong_duong_track_b_pilot_guide.md` và là script Lan (Từ Liêm) đang
dùng thực tế. Xóa file này trong nhánh của Đức không ảnh hưởng ai khác *cho
đến khi* nhánh này được merge vào `main` — lúc đó sẽ làm hỏng luôn tài liệu và
công cụ chung.

**Cách sửa:** khôi phục lại file đúng như trên `main`:
```powershell
git checkout main -- ingest_era5_weather.py
```

**Nguyên tắc chung:** không xóa file dùng chung chỉ vì mình không dùng đến —
nếu thấy file nào có vẻ thừa, hỏi lại nhóm trước khi xóa, đừng tự quyết trong
nhánh riêng của mình.

## Tóm tắt việc cần làm, đúng thứ tự

1. ~~Chạy lại B5/B3~~ — không cần (đã xác minh số liệu đúng).
2. Khôi phục `.gitignore` — **Đức đã làm** (commit `457b342`).
3. Gỡ PBF khỏi Git — **Đức đã làm** (không còn file `.pbf` nào trong cây).
4. Khôi phục `ingest_era5_weather.py` — **Đức đã làm** (giống hệt `main`).
5. Còn lại: xem phần "Việc còn sót" trong báo cáo kiểm tra của leader.
