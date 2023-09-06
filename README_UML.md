[<img src="images\All_game_UML-Copy of Page-1.drawio.png" width="720">](https://github.com/QuocBuu/archery_game.git)

### Ghi chú:
**SCREEN_ENTRY:** Cài đặt các thiết lâp ban đầu cho đối tượng trong game.
- **Level setup:** Thiết lập thông số cấp độ cho game.
- **AR_GAME_ARCHERY_SETUP:** Thiết lập thông số ban đầu cho đối tượng Cung tên (Archery)
- **AR_GAME_ARROW_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Mũi tên (Arrow)
- **AR_GAME_METEOROID_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Thiên thạch (Meteoroid)
- **AR_GAME_BANG_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Vụ nổ (Bang)
- **AR_GAME_BORDER_SETUP:** Thiết lập thông số ban đầu cho đối tượng Ranh giới (Border)
- **Setup timer - Time tick:** Khởi tạo Timer - Time tick cho game.
- **STATUS (GAME_ON):** Cập nhật trạng thái game -> GAME_ON

**GAME PLAY:** Quá trình hoạt động của game.
**GAME PLAY - Nomal:** Game hoạt động ở trạng thái bình thường.
- **AR_GAME_TIME_TICK:** Signal do Timer - Time tick gửi đến.
- **AR_GAME_ARCHERY_UPDATE:** Cập nhật trạng thái Cung tên.
- **AR_GAME_ARROW_RUN:** Mũi tên di chuyển nhưng hiện không có mũi tên được bắn ra nên không cập nhật.
- **AR_GAME_METEOROID_RUN:** Cập nhật việc di chuyển của các Thiên thạch.
- **AR_GAME_METEOROID_DETONATOR:** Kiểm tra các Thiên thạch có bị Mũi tên phá hủy, hiện giờ không có Mũi tên bắn ra nên không cập nhật.
- **AR_GAME_BANG_UPDATE:** Không có Thiên thạch bị phá hủy nên không có Vụ nổ được cập nhật.
- **AR_GAME_BORDER_UPDATE:** Kiểm tra số điểm hiện tại không đủ để cập nhật nên không tăng độ khó game.
- **AR_GAME_CHECK_GAME_OVER:** Kiểm tra Thiên thạch không chạm vào Ranh giới nên không gửi Signal - **AR_GAME_RESET**

**GAME PLAY - Action:** Game hoạt động ở trạng thái có tác động của các nút nhấn.
- **AR_GAME_ARCHERY_UP:** Player nhấn nút **[Up]** điều khiển Cung tên di chuyển lên.
- **AR_GAME_ARCHERY_DOWN:** Player nhấn nút **[Down]** điều khiển Cung tên di chuyển xuống.
- **AR_GAME_ARROW_SHOOT:** Player nhấn nút **[Mode]** điều khiển bắn mũi tên ra.
- **AR_GAME_TIME_TICK:** Signal do Timer - Time tick gửi đến.
- **AR_GAME_ARCHERY_UPDATE:** Cập nhật trạng thái hiện tại của Cung tên sau khi bị điều khiển.
- **AR_GAME_ARROW_RUN:** Đã có mũi tên được bắn ra - Cập nhật trạng thái di chuyển của mũi tên.
- **AR_GAME_METEOROID_RUN:** Cập nhật việc di chuyển của các Thiên thạch. 
- **AR_GAME_METEOROID_DETONATOR:** Kiểm tra Mũi tên bắn trúng Thiên thạch, cộng điểm vào Score, cập nhật trạng thái cho Mũi tên, Thiên thạch và Vụ nổ.
- **AR_GAME_BANG_UPDATE:** Cập nhật trạng thái hiển thị của Vụ nổ - Sau 3 lần cập nhật tự động reset Vụ nổ.
- **AR_GAME_BORDER_UPDATE:** Kiểm tra số điểm hiện tại chia hết cho 200 thì tăng tốc độ Thiên thạch lên và cộng 10 điểm.
- **AR_GAME_CHECK_GAME_OVER:** Kiểm tra Thiên thạch không chạm vào Ranh giới nên không gửi Signal - **AR_GAME_RESET**

**GAME PLAY - LOSE:** Game hoạt động lúc phát hiện Thiên thạch chạm vào Ranh giới.
- **AR_GAME_METEOROID_RUN:** Thiên thạch di chuyển.
- **AR_GAME_CHECK_GAME_OVER:** Kiểm tra thấy có Thiên thạch chạm vào Ranh giới. Gửi Signal - **AR_GAME_RESET** đến Screen

**RESET GAME:** Quá trình cài đặt lại các thông số trước khi thoát game.
- **STATUS (GAME_OVER):** Cập nhật trạng thái game -> GAME_OVER
- **AR_GAME_RESET:** Signal cài đặt lại game do Border gửi đến.
- **AR_GAME_ARCHERY_RESET:** Cài đặt lại đối tượng Cung tên trước khi thoát.
- **AR_GAME_ARROW_RESET:** Cài đặt lại đối tượng Mũi tên trước khi thoát.
- **AR_GAME_METEOROID_RESET:** Cài đặt lại đối tượng Thiên thạch trước khi thoát.
- **AR_GAME_BANG_RESET:** Cài đặt lại đối tượng Vụ nổ trước khi thoát.
- **AR_GAME_BORDER_RESET:** Cài đặt lại đối tượng Ranh giới trước khi thoát.
- **Save and reset Score:** Lưu số điểm hiện tại và Cài đặt lại.
- **Timer remove - Timer tick:** Xóa Timer - Time tick
- **Setup timer - Timer exit:** Tạo 1 timer one shot để thoát game.

**EXIT:** Thoát khỏi game và chuyển sang màn hình khác.
- **AR_GAME_EXIT:** Signal do Timer exit gửi đến.
- **STATUS (GAME_OFF):** Cập nhật trạng thái game -> GAME_OFF
- **Change the screeen:** Chuyển màn hình
