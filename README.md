# Lập trình event-driven và trực quan cho trò chơi bắn cung: Hướng dẫn từ cơ bản đến nâng cao
## I. Giới thiệu - ARCHERY GAME
### 1.1 Mô tả trò chơi và đối tượng
Phần mô tả sau đây về “Archery game” phục vụ mục đích là giải thích cách chơi và các đặc tính kỹ thuật nhằm mục đích thiết kế và triển khai phần mềm và phát triển các dự án game ở phần sau chương trình này.

[<img src="images\menu_game.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

Trò chơi bắt đầu với màn hình Menu game với nhiều chọn lựa: 
- Archery Game: chọn vào để bắt đầu chơi game
- Setting: chọn vào để cài đặt các thông số của game
- Charts: chọn vào để xem top 3 điểm cao nhất đạt được
- Exit: vào màn hình nghỉ

[<img src="images\objects_in_the_game.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

#### Các đối tượng trong game:
- Archey: Cung tên - nơi bắn ra mũi tên
- Arrow: Mũi tên - bắn ra từ cung tên để phá hủy thiên thạch
- Bang: Vụ nổ - xuất hiện khi thiên thạch bị phá hủy
- Border: Ranh giới - vùng an toàn phải bảo vệ không cho thiên thạch rơi vào
- Meteoroid: Thiên thạch - dối tượng bay về phía vùng an toàn có thể phá hủy bởi mũi tên

#### Cách chơi game: 
- Trong game này bạn sẽ điều khiển 1 cung tên (Archery) di chuyển lên xuống chọn vị trí và bắn ra mũi tên (Arrow) tiêu diệt các đối tượng là các thiên thạch (Meteoroid) đang bay đến. Với số lượng mũi tên giới hạn, mỗi lần bắn tên số mũi tên sẽ giảm xuống và không bắn được nếu số mũi tên bằng “0” được hiển thị ở bên dưới góc trái màn hình hiển thị. 
- Với mỗi lần mũi tên (Arrow) bắn trúng thiên thạch (Meteoroid) thì thiên thạch sẽ tạo ra vụ nổ (Bang) và được +10 điểm vào “score” ở bên dưới góc phải màn hình. Và số lượng mũi tên cũng được khôi phục + 1 mũi tên. Ngoài ra khi mũi tên đi hết màn hình thì mũi tên cũng được xóa và khôi phục +1 mũi tên.
- Với mỗi 200 điểm kiếm được thì tốc độ của thiên thạch (Meteoroid) sẽ tăng dần lên tạo độ khó cho game.

[<img src="images\game_over.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

Game sẽ kết thúc khi thiên thạch (Meteoroid) chạm phải ranh giới (Border). Số điểm sẽ được lưu và bạn sẽ vào màn hình “Game Over” bạn có 3 lựa chọn là:
- Restart: chơi lại.
- Charts: vào xem bảng xếp hạng.
- Home: về lại menu game.

### 1.2 Phần cứng

[<img src="images\AK_Embedded_Base_Kit_STM32L151.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

AK Embedded Base Kit là một evaluation kit dành cho các bạn học software embedded nâng cao.

KIT tích hợp LCD Oled 1.3", 3 nút nhấn, và 1 loa Buzzer phát nhạc, với các trang bị này thì đã đủ để học hệ thống event-driven thông qua thực hành thiết kế máy chơi game.

KIT cũng tích hợp RS485, NRF24L01+, và Flash lên đến 32MB, thích hợp cho prototype các ứng dụng thực tế trong hệ thống nhúng hay sử dụng như: truyền thông có dây, không dây wireless, các ứng dụng lưu trữ data logger,...

#### Thao tác trong game:
- Trong game người chơi có thể sử dụng 2 nút **[Up]** và **[Down]** để điều hướng chọn mục hay điều khiển Cung tên (Archery) lên xuống.
- Nút **[Mode]** dùng để chọn vào các mục trong Menu game hay bắn ra các mũi tên lúc chơi game.
- Nút **[Reset]** để khởi động lại kit.

## II. Lập trình hướng đối tượng trong trò chơi bắn cung
### 2.1 Lập trình hướng đối tượng và các khái niệm cơ bản
#### Lập trình hướng đối tượng
Lập trình hướng đối tượng (Object-Oriented Programming - OOP) là một phương pháp lập trình tập trung vào việc xây dựng các đối tượng và tương tác giữa chúng. Trong lập trình hướng đối tượng, các đối tượng được coi là các thực thể trong thế giới thực, có trạng thái và hành vi riêng.

#### Các khái niệm cơ bản
**Lớp (Class):** Lớp là một mô hình hoặc bản thiết kế cho một loại đối tượng cụ thể. Nó định nghĩa các thuộc tính (properties) và phương thức (methods) mà các đối tượng thuộc lớp đó sẽ có.

**Đối tượng (Object):** Đối tượng là một phiên bản cụ thể được tạo ra từ một lớp. Nó có trạng thái (state) duy nhất được xác định bởi các giá trị của các thuộc tính và có thể thực hiện các hành vi

**Thuộc tính (Property):** Thuộc tính là các biến được định nghĩa trong lớp để lưu trữ thông tin về trạng thái của đối tượng. Mỗi đối tượng có thể có các giá trị khác nhau cho các thuộc tính tương ứng.

### 2.2 Mô hình hóa đối tượng trong trò chơi bắn cung
#### Các đối tượng trong trò chơi
Ứng dùng kết hợp giữa lập trình hướng đối tượng và event-driven giúp xác định các thuộc tính có trong đối tượng và chia dối tượng ra làm nhiều event khác nhau để tối ưu hóa quá trình xử lý.

## III. Lập trình event-driven trong trò chơi bắn cung
### 3.1 Lập trình event-driven và các khái niệm cơ bản
#### Lập trình event-driven

[<img src="images\event_driven.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

Trong lập trình event-driven, chương trình được tổ chức xung quanh việc xử lý các sự kiện (events). Mỗi sự kiện đại diện cho một hành động xảy ra trong hệ thống, chẳng hạn như nhấn nút, update hoặc timer. Khi một sự kiện xảy ra, chương trình sẽ phản ứng bằng cách gọi các xử lý sự kiện tương ứng để thực hiện các hành động liên quan. 

Lập trình event-driven tạo điều kiện cho các ứng dụng tương tác, phản hồi nhanh chóng và linh hoạt.

#### Các khái niệm trong event-driven
**Sự kiện (Event):** Sự kiện đại diện cho một hành động xảy ra trong hệ thống. Trong trò chơi, các sự kiện có thể là các hành động của người chơi như nhấn nút, timer, cập nhật trạng thái cho đối tượng.

**Xử lý sự kiện (Event Handling):** Xử lý sự kiện là quá trình xác định cách chương trình phản ứng khi một sự kiện xảy ra. Điều này bao gồm việc cung cấp mã xử lý (event handler) để thực hiện các hành động tương ứng với sự kiện. Mã xử lý được gắn kết với sự kiện và sẽ được gọi tự động khi sự kiện xảy ra.

**Task:** Task đại diện cho một tác vụ hoặc một công việc cần được thực hiện trong hệ thống. Trong lập trình event-driven, mỗi sự kiện thường gắn kết với một task tương ứng. Khi một sự kiện xảy ra, hệ thống tạo ra một task tương ứng và giao nó cho bộ xử lý để thực hiện.

**Signal:** Signal là một cơ chế truyền thông tin giữa các thành phần trong hệ thống event-driven. Khi một sự kiện xảy ra, nó có thể gửi một tín hiệu (signal) để thông báo cho các thành phần khác về việc xảy ra của sự kiện đó. Signal có thể chứa các thông tin cần thiết như loại sự kiện, đối tượng liên quan, hay các tham số khác.

### 3.2 Xử lý sự kiện trong trò chơi bắn cung

**Sơ đồ trình tự được sử dụng để mô tả và hiển thị trình tự của các thông điệp và tương tác giữa các đối tượng trong trò chơi**

[<img src="images\sequence_archery_game.png" width="720"/>](https://github.com/QuocBuu/archery_game.git)

#### Mô tả: Sơ đồ trình tự trên mô tả 3 trường hợp xử lý xự kiện của hệ thông:
- Time tick – Common: mô phỏng trạng thái của game lúc hoạt động bình thường.
- Time tick – Control: mô phỏng trạng thái của game lúc có các tác động điều khiển qua nút nhấn.
- Time tick – Game over: mô phỏng trạng thái xử lý lúc game over.

#### Chú thích: 

#### Các task sử dụng trong game:
|Tasks ID               | Task level        | App tasks                 |
|---                    |---                |---                        |
|AR_GAME_ARCHERY_ID     | TASK_PRI_LEVEL_4  | ar_game_archery_handle    |
|AR_GAME_ARROW_ID       | TASK_PRI_LEVEL_4  | ar_game_arrow_handle      |
|AR_GAME_BANG_ID        | TASK_PRI_LEVEL_4  | ar_game_bang_handle       |
|AR_GAME_BORDER_ID      | TASK_PRI_LEVEL_4  | ar_game_border_handle     |
|AR_GAME_METEOROID_ID   | TASK_PRI_LEVEL_4  | ar_game_meteoroid_handle  |
|AR_GAME_SCREEN_ID      | TASK_PRI_LEVEL_4  | scr_archer_game_handle    |

## IV. Kết hợp lập trình hướng đối tượng, event-driven trong trò chơi bắn cung
### 4.1 Sự tương tác giữa lập trình hướng đối tượng và event-driven trong trò chơi

**Kết hợp giữa lập trình hướng đối tượng (OOP) và event-driven:** Trong trò chơi này, sử dụng cách chia **đối tượng** và tổ chức **thuộc tính** trong đối tượng của "_lập trình hướng đối tượng_". Đồng thời sử dụng phương pháp xử lý hệ thống, độc lập từng phần thông qua **task** và **signal** của "_event-driven_"

**Ưu điểm:**
**Quản lý trạng thái dễ dàng:** Khi kết hợp với kiểm xử lý task và signal trong mô hình event-driven, sử dụng đối tượng và thuộc tính cho phép quản lý trạng thái của chương trình một cách dễ dàng.

**Tính mở rộng và linh hoạt:** Kết hợp đối tượng và thuộc tính với kiểm xử lý task và signal cung cấp tính mở rộng và linh hoạt cao. Bằng cách tạo ra các lớp đối tượng và sử dụng các thuộc tính để định nghĩa trạng thái và hành vi, bạn có thể dễ dàng mở rộng chương trình bằng cách thêm các lớp và thuộc tính mới. Điều này giúp tăng tính linh hoạt và khả năng mở rộng của chương trình

**Tính tái sử dụng cao:** Việc sử dụng đối tượng và thuộc tính trong lập trình hướng đối tượng cho phép tái sử dụng mã nguồn dễ dàng.

**Tính mô đun và dễ bảo trì:** Sử dụng mô hình lập trình hướng đối tượng giúp tách biệt logic và chức năng của chương trình thành các đối tượng riêng biệt.

### 4.2 Triển khai lập trình hướng đối tượng, event-driven và tính năng trực quan trong trò chơi bắn cung
#### 4.2.1 Cung tên (Archery)

**Mô tả:** Cung tên là đối tượng cung tên có thể di chuyển lên, xuống trong trò chơi được điều khiển bởi 2 nút **[Up],[Down]** trên Kit

**Thuộc tính:** 
- Vị trí tạo độ: x (_uint32_t_), y (_uint32_t_)
- Hiển thị: visible (_bool_)
- Animation: action_image (_uint8_t_) 

**Task:**
- Task ID: AR_GAME_ARCHERY_ID
- Task level: TASK_PRI_LEVEL_4
- App task: ar_game_archery_handle

**Signal:**
- AR_GAME_ARCHERY_SETUP - Cài đặt thuộc tính ban đầu
- AR_GAME_ARCHERY_UPDATE - Cập nhật thuộc tính theo **Timer**
- AR_GAME_ARCHERY_UP - Nhận tín hiệu nút nhấn **[Up]**
- AR_GAME_ARCHERY_DOWN - Nhận tín hiệu nút nhấn **[Down]**
- AR_GAME_ARCHERY_RESET - Cài đặt lại thuộc tính trước khi **End Game**

**Sequence diagram:**
[<img src="images\archery_sequence.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

#### 4.2.2 Mũi tên (Arrow)

**Mô tả:** Mũi tên là đối tượng bắn ra từ cung tên, từ lúc bắn ra sẽ liên tục di chuyển từ trái sang phải màn hình. Mũi tên có thể phá hủy thiên thạch trên đường đi. Số lượng mũi tên hiển thị ở góc dưới bên trái màn hình.

**Thuộc tính:** 
- Vị trí tạo độ: x (_uint32_t_), y (_uint32_t_)
- Hiển thị: visible (_bool_)
- Animation: action_image (_uint8_t_) 

**Task:**
- Task ID: AR_GAME_ARROW_ID
- Task level: TASK_PRI_LEVEL_4
- App task: ar_game_arrow_handle

**Signal:**
- AR_GAME_ARROW_SETUP - Cài đặt thuộc tính ban đầu
- AR_GAME_ARROW_RUN - Cập nhật thuộc tính theo **Timer**
- AR_GAME_ARROW_SHOOT - Nhận tín hiệu nút nhấn **[Mode]**
- AR_GAME_ARROW_RESET - Cài đặt lại thuộc tính trước khi **End Game**

**Sequence diagram:**
[<img src="images\arrow_sequence.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

#### 4.2.3 Vụ nổ (Bang)

**Mô tả:** Vụ nổ là đối tượng xuất hiện sau khi Thiên thạch bị phá hủy bởi Mũi tên. Vụ nổ xuất hiện ở vị trí nơi Thiên thạch bị phá hủy với 3 hoạt ảnh vụ nổ thay đổi theo thới gian đi kèm với âm thanh nổ rồi kết thúc.

**Thuộc tính:** 
- Vị trí tạo độ: x (_uint32_t_), y (_uint32_t_)
- Hiển thị: visible (_bool_)
- Animation: action_image (_uint8_t_) 

**Task:**
- Task ID: AR_GAME_BANG_ID
- Task level: TASK_PRI_LEVEL_4
- App task: ar_game_bang_handle

**Signal:**
- AR_GAME_BANG_SETUP - Cài đặt thuộc tính ban đầu
- AR_GAME_BANG_UPDATE - Cập nhật thuộc tính theo **Timer**
- AR_GAME_BANG_RESET - Cài đặt lại thuộc tính trước khi **End Game**

**Sequence diagram:**
[<img src="images\bang_sequence.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)


#### 4.2.4 Ranh giới (Border)

**Mô tả:** Ranh giới là vùng an toàn cần được bảo vệ. Nếu Thiên thạch đi vào trong ranh giới thì sẽ xử lý thua game. Đồng thời trong đối tượng ranh giới sẽ kiểm tra số điểm để cập nhật tăng độ khó cho game.

**Thuộc tính:** 
- Vị trí tạo độ: x (_uint32_t_), y (_uint32_t_)
- Hiển thị: visible (_bool_)
- Animation: action_image (_uint8_t_) 

**Task:**
- Task ID: AR_GAME_BORDER_ID
- Task level: TASK_PRI_LEVEL_4
- App task: ar_game_border_handle

**Signal:**
- AR_GAME_BORDER_SETUP - Cài đặt thuộc tính ban đầu
- AR_GAME_BORDER_UPDATE - Kiểm tra số điểm (score) để cập nhật độ khó
- AR_GAME_BORDER_RESET - Cài đặt lại thuộc tính trước khi **End Game**
- AR_GAME_CHECK_GAME_OVER - Kiểm tra thiên thạch chạm vào ranh giới

**Sequence diagram:**
[<img src="images\border_sequence.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

#### 4.2.5 Thiên thạch (Meteoroid)

**Mô tả:** Thiên thạch là đối tượng xuất hiện thường xuyên trên màn hình luôn di chuyển từ Phải sang Trái. Vị trí xuất hiện có tính ngẫu nghiên có kiểm soát. Thiên thạch có thể bị phá hủy bởi Mũi tên, sau khi nổ xong tự động xuất hiện lại ngẩu nhiên.

**Thuộc tính:** 
- Vị trí tạo độ: x (_uint32_t_), y (_uint32_t_)
- Hiển thị: visible (_bool_)
- Animation: action_image (_uint8_t_) 

**Task:**
- Task ID: AR_GAME_METEOROID_ID
- Task level: TASK_PRI_LEVEL_4
- App task: ar_game_meteoroid_handle

**Signal:**
- AR_GAME_METEOROID_SETUP - Cài đặt thuộc tính ban đầu
- AR_GAME_METEOROID_RUN - Cập nhật thuộc tính theo **Timer**
- AR_GAME_METEOROID_DETONATOR - Kiểm tra mũi tên có chạm phải thiên thạch không
- AR_GAME_METEOROID_RESET - Cài đặt lại thuộc tính trước khi **End Game**

**Sequence diagram:**
[<img src="images\meteoroid_sequence.png" width="480"/>](https://github.com/QuocBuu/archery_game.git)

#### 4.2.6 Màn hình trò chơi (Screen archery game)

**Mô tả:** Màn hình trò chơi là nơi cấp phát **signal** cho các đối tượng. Là nơi tiếp nhận **Data** của các đối tượng để hiển thị ra màn hình LCD. Cũng là nơi đọc bộ nhớ **EEPROM** để lấy các thiết lập **Level**

**Thuộc tính:** 
- Status: ar_game_status (_uint8_t_)
- Setup level: settingsetup (_ar_game_setting_t_)

**Task:**
- Task ID: AR_GAME_SCREEN_ID
- Task level: TASK_PRI_LEVEL_4
- App task: scr_archery_game_handle

**Signal:**
- SCREEN_ENTRY - Gửi **signal** cho các đối tượng và khởi tạo **Timer**
- AR_GAME_TIME_TICK - Gửi **signal** cập nhật thuộc tính theo **Timer** cho các **đối tượng**
- AR_GAME_RESET - Gửi **signal** cài đặt lại thuộc tính cho các đối tượng
- AR_GAME_EXIT_GAME - Lưu điểm và chuyển màn hình **Game Over**

**Sequence diagram:**
[<img src="images\screen_sequence.png" width="720"/>](https://github.com/QuocBuu/archery_game.git)

## V. Hiển thị và âm thanh trong trò chơi bắn cung
### 5.1 Đồ họa

Trong trò chơi, màn hình hiện thị là 1 màn hình LCD có kích thước là 128*64px. Nên các đối tượng được hiển thị trong game phải có kích thước hiển thị phù hợp với màn hình nên cần được thiết kế riêng. 

Đồ họa được thiết kế từng phần theo từng đối tượng bằng phần mềm [Photopea](https://www.photopea.com/)

#### Thiết kế đồ họa cho các đối tượng
[<img src="images\bitmap.png" width="720"/>](https://github.com/QuocBuu/archery_game.git)

**Ghi chú:** trong thiết kế trên có nhiều hoạt ảnh cho cùng 1 đối tượng là để tạo animation cho đối tượng đó tăng cảm giác lúc chơi game.

### 5.2 Âm thanh
Âm thành được thiết kế qua wed [Arduino Music](https://www.instructables.com/Arduino-Music-From-Sheet-Music/)

Trong trò chơi, để trò chơi thêm phần xin động thì việc có âm thanh là điều cần thiết. 

Các âm thanh cần thiết kế: nút nhấn, bắn tên, vụ nổ, nhạc game.

**Ghi chú:** Nêu không có thời gian hay không có kiếu âm nhạc thì tốt nhất nên dùng các thư viện trên github

## VI. Kết luận
### 6.1 Tóm tắt nội dung
Tài liều tóm tắt về quá trình làm 1 con game trên AK Embedded Base Kit - STM32L151

Đi sau vào 2 nội dung chính là:
- Mô tả cách mô hình hóa đối tượng trong trò chơi bắn cung.
- Sử dụng kết hợp lập trình hướng đối tượng và lập trình dựa trên sự kiện (event-driven) để xác định thuộc tính và xử lý các sự kiện trong trò chơi.

