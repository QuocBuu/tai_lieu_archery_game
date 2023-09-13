# Lập trình event-driven - Áp dụng cho trò chơi bắn cung: Hướng dẫn từ cơ bản đến nâng cao
## I. Giới thiệu - ARCHERY GAME
### 1.1 Phần cứng

<center><img src="images\AK_Embedded_Base_Kit_STM32L151.png" width="480"/>

*Hình 1: AK Embedded Base Kit - STM32L151* </center>

AK Embedded Base Kit là một công cụ đánh giá dành cho các bạn học phần mềm nhúng nâng cao.

KIT tích hợp LCD Oled 1.3", 3 nút nhấn, và 1 loa Buzzer phát nhạc, với các trang bị này thì đã đủ để học hệ thống event-driven thông qua thực hành thiết kế máy chơi game.

KIT cũng tích hợp RS485, NRF24L01+, và Flash lên đến 32MB, thích hợp cho prototype các ứng dụng thực tế trong hệ thống nhúng hay sử dụng như: truyền thông có dây, không dây wireless, các ứng dụng lưu trữ data logger,...

### 1.2 Mô tả trò chơi và đối tượng
Phần mô tả sau đây về “Archery game” là giải thích cách chơi và cơ chế xử lý của trò chơi. Nhằm phục vụ cho việc thiết kế và phát triển trò chơi về sau.

<center><img src="images\menu_game.png" width="480"/>

*Hình 2: Menu game* </center>

Trò chơi bắt đầu với màn hình **Menu game** với nhiều chọn lựa: 
- **Archery Game:** chọn vào để bắt đầu chơi game
- **Setting:** chọn vào để cài đặt các thông số của game
- **Charts:** chọn vào để xem top 3 điểm cao nhất đạt được
- **Exit:** vào màn hình nghỉ

<center><img src="images\objects_in_the_game.png" width="480"/>

*Hình 3: Màn hình Game_play và các đối tượng* </center>

#### Các đối tượng trong game:
|Tên đối tượng|Tác dụng|
|---|---|
|**Archey** - Cung tên|Nơi bắn ra mũi tên|
|**Arrow** - Mũi tên|Bắn ra từ cung tên để phá hủy thiên thạch|
|**Bang** - Vụ nổ |Xuất hiện khi thiên thạch bị phá hủy|
|**Border** - Ranh giới |Vùng an toàn phải bảo vệ không cho thiên thạch rơi vào|
|**Meteoroid** - Thiên thạch |Đối tượng bay về phía vùng an toàn có thể phá hủy bởi mũi tên|

(*) Trong phần còn lại của tài liệu sẽ dùng tên của các đối tượng để đề cập đến đối tượng.

#### Cách chơi game: 
- Trong trò chơi này bạn sẽ điều khiển Archery di chuyển **lên/xuống** bằng hai nút **[Up]/[Down]** chọn vị trí **bắn ra** Arrow bằng nút **[Mode]** nhằm phá hủy các Meteoroid đang bay đến.

- Mục tiêu trò chơi là kiếm được nhiều điểm nhất có thể, trò chơi sẽ kết thúc khi có Meteoroid chạm vào Border.

#### Cơ chế hoạt động:
- Cách tính điểm: Điểm được tính bằng số lượng Meteoroid phá hủy được. Với mỗi Meteoroid bị phá hủy cộng 10 điểm vào score. Số điểm sẽ được hiển thị ở góc dưới bên phải màn hình.
- Giới hạn: Số lượng Arrow được giới hạn có thể thay đổi trong phần **setting** mỗi khi bắn ra số lượng Arrow giảm nếu về "0" thì không bắn ra được và có âm thanh báo. Số lượng mũi tên sẽ được hồi lại khi Arrow phá hủy Meteoroid hoặc Arrow đi hết màn hình trò chơi. Số lượng Arrow được hiển thị ở góc dưới bên trái màn hình.
- Lên level: Để tạo độ khó cho game thì chứ mỗi lần đạt được 200 điểm thì tốc độ của Meteoroid.
- Animation: Để trò chơi thêm phần sinh động thì các đối tượng sẽ có thêm hoạt ảnh lúc di chuyển.

<center>[<img src="images\game_over.png" width="480"/>]

*Hình 4: Màn hình Game_over* </center>

Khi Meteoroid chạm vào Border, trò chơi sẽ kết thúc. Các đối tượng sẽ được reset và số điểm sẽ được lưu và bạn sẽ vào màn hình “Game Over” bạn có 3 lựa chọn là:
- **Restart:** chơi lại.
- **Charts:** vào xem bảng xếp hạng.
- **Home:** về lại menu game.

## II. Thiết kế - ARCHERY GAME
**Các khái niệm trong event-driven:**

**Event:** Sự kiện là một hoạt động xảy ra trong hệ thống. Sự kiện có thể được khởi tạo bởi người dùng hoặc tự động xảy ra trong chương trình.

**Event Handling:** Xử lý sự kiện là quá trình xác định cách chương trình phản ứng khi một sự kiện xảy ra. Điều này bao gồm việc cung cấp mã xử lý (event handler) để thực hiện các hành động tương ứng với sự kiện. Mã xử lý được gắn kết với sự kiện và sẽ được gọi tự động khi sự kiện xảy ra.

### 2.1 Sơ đồ trình tự
**Sơ đồ trình tự** được sử dụng để mô tả và hiển thị trình tự của các thông điệp và tương tác giữa các đối tượng trong một hệ thống.

<center><img src="images\sequence_object\All_game_UML.png" width="720"/>

*Hình 5: The sequence diagram* </center>

### Ghi chú:
**SCREEN_ENTRY:** Cài đặt các thiết lâp ban đầu cho đối tượng trong game.
- **Level setup:** Thiết lập thông số cấp độ cho game.
- **AR_GAME_ARCHERY_SETUP:** Thiết lập thông số ban đầu cho đối tượng Archery
- **AR_GAME_ARROW_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Arrow
- **AR_GAME_METEOROID_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Meteoroid
- **AR_GAME_BANG_SETUP:** Thiết lập thông số ban đầu cho các đối tượng Bang
- **AR_GAME_BORDER_SETUP:** Thiết lập thông số ban đầu cho đối tượng Border
- **Setup timer - Time tick:** Khởi tạo Timer - Time tick cho game.
- **STATUS (GAME_ON):** Cập nhật trạng thái game -> GAME_ON

**GAME PLAY:** Quá trình hoạt động của game.

**GAME PLAY - Nomal:** Game hoạt động ở trạng thái bình thường.
- **AR_GAME_TIME_TICK:** Signal do Timer - Time tick gửi đến.
- **AR_GAME_ARCHERY_UPDATE:** Cập nhật trạng thái Archery.
- **AR_GAME_ARROW_RUN:** Cập nhật di chuyển của các Arrow theo thời gian.
- **AR_GAME_METEOROID_RUN:** Cập nhật di chuyển của các Meteoroid theo thời gian.
- **AR_GAME_METEOROID_DETONATOR:** Kiểm tra các Meteoroid có bị Arrow phá hủy.
- **AR_GAME_BANG_UPDATE:** Cập nhật hoạt ảnh Vụ nổ theo thời gian
- **AR_GAME_BORDER_UPDATE:** Kiểm tra số điểm hiện tại để cập nhật tăng độ khó game.
- **AR_GAME_CHECK_GAME_OVER:** Kiểm tra Meteoroid chạm vào Border nếu chạm vào thì gửi Signal - **AR_GAME_RESET** đến **Screen**

**GAME PLAY - Action:** Game hoạt động ở trạng thái có tác động của các nút nhấn.
- **AR_GAME_ARCHERY_UP:** Player nhấn nút **[Up]** điều khiển Archery di chuyển lên.
- **AR_GAME_ARCHERY_DOWN:** Player nhấn nút **[Down]** điều khiển Archery di chuyển xuống.
- **AR_GAME_ARROW_SHOOT:** Player nhấn nút **[Mode]** điều khiển bắn Arrow ra.

**GAME PLAY - LOSE:** Game hoạt động lúc phát hiện Meteoroid chạm vào Border.
- **AR_GAME_CHECK_GAME_OVER:** Kiểm tra thấy có Meteoroid chạm vào Border. Gửi Signal - **AR_GAME_RESET** đến **Screen**

**RESET GAME:** Quá trình cài đặt lại các thông số trước khi thoát game.
- **STATUS (GAME_OVER):** Cập nhật trạng thái game -> GAME_OVER
- **AR_GAME_RESET:** Signal cài đặt lại game do Border gửi đến.
- **AR_GAME_ARCHERY_RESET:** Cài đặt lại đối tượng Cung tên trước khi thoát.
- **AR_GAME_ARROW_RESET:** Cài đặt lại đối tượng Arrow trước khi thoát.
- **AR_GAME_METEOROID_RESET:** Cài đặt lại đối tượng Meteoroid trước khi thoát.
- **AR_GAME_BANG_RESET:** Cài đặt lại đối tượng Vụ nổ trước khi thoát.
- **AR_GAME_BORDER_RESET:** Cài đặt lại đối tượng Border trước khi thoát.
- **Save and reset Score:** Lưu số điểm hiện tại và Cài đặt lại.
- **Timer remove - Timer tick:** Xóa Timer - Time tick
- **Setup timer - Timer exit:** Tạo 1 timer one shot để thoát game.

**EXIT:** Thoát khỏi game và chuyển sang màn hình khác.
- **AR_GAME_EXIT:** Signal do Timer exit gửi đến.
- **STATUS (GAME_OFF):** Cập nhật trạng thái game -> GAME_OFF
- **Change the screeen:** Chuyển màn hình


### 2.2 Chi tiết đối tượng

Sau khi xác định được các đối tượng trong game mà chúng ta cần, tiếp theo chúng ta phải liệt kê ra các thuộc tính, các task, các signal và bitmap mà trong game sẽ sử dụng tới.
Việc liệt kê càng chi tiết thì việc làm game diễn ra càng nhanh và tạo tình rõ ràng minh bạch cho phần tài nguyên giúp phần code game diễn ra xuông sẽ hơn.

#### 2.2.1	Thuộc tính đối tượng - struct
Việc liệt kê các thuộc tính của đối tượng trong game có các tác dụng quan trọng sau:
- Liệt kê các thuộc tính giúp xác định rõ thông tin về đối tượng trong game.
- Liệt kê thuộc tính giúp xác định cấu trúc dữ liệu phù hợp để lưu trữ thông tin của đối tượng.
- Khi bạn xác định trước các thuộc tính cần thiết, bạn giảm thiểu khả năng bỏ sót hoặc nhầm lẫn trong việc xử lý và sử dụng các thuộc tính.

**Trạng thái** của một đối tượng được biểu diễn bởi các **thuộc tính**. Trong trò chơi này các đối tượng có các Thuộc tính cụ thể là:
- visible: Quy định hiển thị của đối tượng.
- x, y: Quy định vị trí của đối tượng trên màn hình.
- action_image: Quy định hoạt ảnh tạo animation.

**Các biến quan trọng:**
- ar_game_score: Điểm của trò chơi
- ar_game_status: Trạng thái quả trò chơi
  - GAME_OFF: Tắt 
  - GAME_ON: Bật
  - GAME_OVER: Đã thua

- **ar_game_setting_t** settingsetup : cấp độ trò chơi
  - settingsetup.silent : Quy định âm thanh ON/OFF
  - settingsetup.num_arrow : Số lượng mũi tên
  - settingsetup.arrow_speed : Tốc độ mũi tên
  - settingsetup.meteoroid_speed : Tốc độ của thiên thạch

<!-- [<img src="images\table_variable.png" width="720720"/>](https://github.com/QuocBuu/archery_game.git)

__(*)__ action_image là 1 biến chuyên dụng để chuyển animation, có thể cần thiết với các game muốn sinh động còn không có thể bỏ qua
__(**)__ giá trị ban đầu được lưu trữ trong eeprom để không bị mất lúc reset kit -->

#### 2.2.2	Task
Trong lập trình event-driven, một task là một đơn vị công việc độc lập được thực thi khi xảy ra một sự kiện cụ thể. Tác dụng của task là xử lý và đáp ứng cho các sự kiện trong hệ thống. Một số tác dụng quan trọng của task:
- **Xử lý sự kiện:** Task được sử dụng để xử lý các sự kiện khi chúng xảy ra. Mỗi task có thể được liên kết với một sự kiện cụ thể và thực thi một loạt các hành động khi sự kiện đó xảy ra.
- **Đồng bộ hóa:** Task cung cấp cơ chế đồng bộ hóa cho việc xử lý các sự kiện. Khi một sự kiện xảy ra, task tương ứng được kích hoạt và thực thi. Các task khác có thể đợi cho đến khi task hiện tại hoàn thành trước khi được kích hoạt. Điều này giúp đảm bảo rằng các hành động xử lý sự kiện được thực hiện theo một thứ tự nhất định và tránh xung đột.
- **Quản lý luồng điều khiển:** Task cho phép quản lý luồng sự kiện trong ứng dụng event-driven. Bằng cách sử dụng task, bạn có thể xác định thứ tự thực thi của các hành động khi xảy ra các sự kiện khác nhau.
- **Tách biệt logic:** Sử dụng task giúp tách biệt logic xử lý sự kiện. Điều này giúp tăng tính sạch sẽ, dễ đọc.
- **Phân cấp nhiệm vụ:** Task level cho phép việc xắp xếp trình tự ưu tiên xử lý của task. Trong game các task level của game điều là 4 nên task nào được gọi trước sẽ xử lý trước. 

<center><img src="images\table_task.png" width="480"/>

*Hình 6: Bảng Task của các đối tượng* </center>

#### 2.2.3	Signal
**Signal** là một cơ chế truyền thông tin giữa các thành phần trong hệ thống event-driven. Khi một sự kiện xảy ra, nó có thể gửi một signal để thông báo cho các thành phần khác về việc xảy ra của sự kiện đó. 

<center><img src="images\table_signal.png" width="480"/>

*Hình 7: Bảng Signal của từng Task* </center>

(*) Tác dụng của các Signal: xem tại Ghi chú - Hình 5

## III. Hướng dẫn chi tiết code trong đối tượng
### 3.1 Archery
**Sequence diagram:**

<center><img src="images\sequence_object\archery_sequence.png" width="480"/>

*Hình 8: Archery sequence* </center>

**Tóm tắt nguyên lý:** Archery sẽ nhận Signal thông được gửi từ 2 nguồn là Screen và Buttton. Quá trình xử lý của đối tượng phần làm 3 giai đoạn:
- Giai đoạn 1: Bắt đầu game, cài đặt các thông số của Archery như vị trí và hình ảnh.
- Giai đoạn 2: Chơi game, trong giai đoạn này chia làm 2 hoạt động là:
  - Cập nhật: Screen gửi Signal cập nhật cho Archery mỗi 100ms để cập nhật trạng thái hiện tại của Archery.
  - Hành động: Button gửi Signal di chuyển lên/xuống cho Archery mỗi khi nhấn nút.
- Giai đoạn 3: Kết thúc game, thực hiện cài đặt lại trạng thái của Archery trước khi thoát game.

**Code:**

Trong code bạn có thể dùng cấu trúc:

    #define TEN_DOAN_CODE()
    do { \
        /*code*/ \
    } while(0);

    Cấu trúc này có thể thay thế hàm void trong nhiều trường hợp.

Khi code về 1 đối tượng ta nên lập ra cho đối tượng 1 struct bao gồm các thuộc tính của để dễ trong việc sử dụng:

    typedef struct {
        bool visible;
        uint32_t x, y;
        uint8_t action_image;
    } ar_game_archery_t;

    extern ar_game_archery_t archery;

Khi viết code trong đối tượng nếu muốn rõ rành và rành mạch bạn có thể đưa các phần code dài ra ngoài khỏi Handle tạo thành các #define như đoạn code dưới như vậy thì khi nhìn vào Handle chúng ta sẽ không bị rối.

```sh
#include "ar_game_archery.h"

ar_game_archery_t archery;
static uint32_t archery_y = AXIS_Y_ARCHERY;

#define AR_GAME_ARCHERY_SETUP() \
do { \
    archery.x = AXIS_X_ARCHERY; \
    archery.y = AXIS_Y_ARCHERY; \
    archery.visible = WHITE; \
    archery.action_image = 1; \
} while (0);

#define AR_GAME_ARCHERY_UP() \
do { \
    archery_y -= STEP_ARCHERY_AXIS_Y; \
    if (archery_y == 0) {archery_y = 10;} \
} while(0);

#define AR_GAME_ARCHERY_DOWN() \
do { \
    archery_y += STEP_ARCHERY_AXIS_Y; \
    if (archery_y > 50) {archery_y = 50;} \
} while(0);

#define AR_GAME_ARCHERY_RESET() \
do { \
    archery.x = AXIS_X_ARCHERY; \
    archery.y = AXIS_Y_ARCHERY; \
    archery.visible = BLACK; \
    archery_y = AXIS_Y_ARCHERY; \
} while(0);

void ar_game_archery_handle(ak_msg_t* msg) {
    switch (msg->sig) {
    case AR_GAME_ARCHERY_SETUP: {
        APP_DBG_SIG("AR_GAME_ARCHERY_SETUP\n");
        AR_GAME_ARCHERY_SETUP();
    }
        break;

    case AR_GAME_ARCHERY_UPDATE: {
        APP_DBG_SIG("AR_GAME_ARCHERY_UPDATE\n");
        archery.y = archery_y;
    }
        break;

    case AR_GAME_ARCHERY_UP: {
        APP_DBG_SIG("AR_GAME_ARCHERY_UP\n");
        AR_GAME_ARCHERY_UP();
    }
        break;

    case AR_GAME_ARCHERY_DOWN: {
        APP_DBG_SIG("AR_GAME_ARCHERY_DOWN\n");
        AR_GAME_ARCHERY_DOWN();
    }
        break;

    case AR_GAME_ARCHERY_RESET: {
        APP_DBG_SIG("AR_GAME_ARCHERY_RESET\n");
        AR_GAME_ARCHERY_RESET();
    }
        break;

    default:
        break;
    }
}
```

### 3.2 Arrow

**Sequence diagram:**

<center><img src="images\sequence_object\arrow_sequence.png" width="480"/>

*Hình 9: Arrow sequence* </center>

**Tóm tắt nguyên lý:** Arrow sẽ nhận Signal thông được gửi từ 2 nguồn là Screen và Buttton. Quá trình xử lý của đối tượng phần làm 3 giai đoạn:
- Giai đoạn 1: Bắt đầu game, cài đặt các thông số của Arrow. Tất cả Arrow vào trạng thái lặn không hiển thị trên màn hình.
- Giai đoạn 2: Chơi game, trong giai đoạn này chia làm 2 hoạt động là:
  - Cập nhật: Screen gửi Signal di chuyển cho Arrow mỗi 100ms để tăng trạng thái của Arrow tạo sự di chuyển cho Arrow.
  - Hành động: Button gửi Signal bắn tên cho Arrow mỗi khi nhấn nút. Arrow sẽ sẽ kiểm tra số mũi tên và nếu còn thì sẽ cập nhật trạng thái để bắn mũi tên ra tại vị trí hiện tại của Archery
- Giai đoạn 3: Kết thúc game, thực hiện cài đặt lại trạng thái của Arrow trước khi thoát game.

**Code:** Tương tự Archery (link tham khảo [Archey_game](https://github.com/QuocBuu/archery_game.git))

### 3.3 Bang

**Sequence diagram:**

<center><img src="images\sequence_object\bang_sequence.png" width="480"/>

*Hình 10: Bang sequence* </center>

**Tóm tắt nguyên lý:** Bang sẽ nhận Signal thông được gửi từ Screen. Quá trình xử lý của đối tượng phân làm 3 giai đoạn:
- Giai đoạn 1: Bắt đầu game, cài đặt các thông số của Bang. Cho tất cả các bang về trạng thái lặn không xuất hiện trên màn hình.
- Giai đoạn 2: Chơi game, Vụ nổ chỉ xuất sau khi Meteoroid bị phá hủy. Vụ nổ bao gồm các hoạt ảnh được cập nhật sau mỗi 100ms sau 3 hoạt ảnh thì sẽ tự reset.
- Giai đoạn 3: Kết thúc game, thực hiện cài đặt lại trạng thái của Arrow trước khi thoát game.

**Code:** Tương tự Archery (link tham khảo [Archey_game](https://github.com/QuocBuu/archery_game.git))

### 3.4 Border

**Sequence diagram:**

<center><img src="images\sequence_object\border_sequence.png" width="480"/>

*Hình 11: Border sequence* </center>

**Tóm tắt nguyên lý:** Border là 1 đối tượng bất động trong game sau khi hiển thi xong thì không cập nhật trạng thái khác nên tôi đặt vào đây thêm 2 nhiệm vụ là Update_level và Check_game_over.
- Giai đoạn 1: Bắt đầu game, cài đặt thông số vị trí và hiển thị của Border.
- Giai đoạn 2: Chơi game, thực hiện các nhiệm vụ theo chu kỳ 100ms
  - Kiểm tra số điểm nếu số điểm thêm 200 thì tăng tốc độ của Meteoroid.
  - Kiểm tra vị trí của các Meteoroid nếu Meteoroid chạm vào Border thì gửi tín hiệu Reset đến Screen
- Giai đoạn 3: Kết thúc game, thực hiện cài đặt lại trạng thái của Border trước khi thoát game.

**Code:** Tương tự Archery (link tham khảo [Archey_game](https://github.com/QuocBuu/archery_game.git))

###  3.5 Meteoroid

**Sequence diagram:**

<center><img src="images\sequence_object\meteoroid_sequence.png" width="480"/>

*Hình 12: Meteoroid sequence* </center>

**Tóm tắt nguyên lý:** Meteoroid là đối tượng xuất hiện và di chuyển liên tục trong game nhận signal từ Screen. Chia làm 3 giai đoạn:
- Giai đoạn 1: Bắt đầu game, cài đặt thông số của Meteoroid. Cấp điểm xuất phát ngẫu nghiên cho Meteoroid, hiển thị lên màn hình.
- Giai đoạn 2: Chơi game, thực hiện các nhiệm vụ theo chu kỳ 100ms
  - Cập nhật vị trí và hoạt ảnh di chuyển cho Meteoroid
  - Kiểm tra vị trí của các Arrow nếu Arrow chạm vào Meteoroid thì thực hiện reset Arrow và Meteoroid rồi tạo Bang.
- Giai đoạn 3: Kết thúc game, thực hiện cài đặt lại trạng thái của Meteoroid trước khi thoát game.

**Code:** Tương tự Archery (link tham khảo [Archey_game](https://github.com/QuocBuu/archery_game.git))

## IV. Hiển thị và âm thanh trong trò chơi bắn cung
### 4.1 Đồ họa

Trong trò chơi, màn hình hiện thị là 1 màn hình LCD có kích thước là 128*64px. Nên các đối tượng được hiển thị trong game phải có kích thước hiển thị phù hợp với màn hình nên cần được thiết kế riêng. 

Đồ họa được thiết kế từng phần theo từng đối tượng bằng phần mềm [Photopea](https://www.photopea.com/)

#### Thiết kế đồ họa cho các đối tượng

<center><img src="images\table_bitmap.png" width="720"/>

*Hình 13: Bitmap của các đối tượng* </center>

**Bitmap** là một cấu trúc dữ liệu được sử dụng để lưu trữ và hiển thị hình ảnh trong game.

**Animation** là ứng dụng việc nối ảnh của của nhiều ảnh liên tiếp tạo thành hoạt ảnh cho đổi tượng muốn miêu tả. Trong game này tôi dùng biến “action_image” trong đối tượng để thay đổi hoạt ảnh thành animation.

**Ghi chú:** trong thiết kế trên có nhiều hoạt ảnh cho cùng 1 đối tượng là để tạo animation cho đối tượng đó tăng cảm giác lúc chơi game.

**Code:**

<details>

**Archer display:**
```sh
void ar_game_archery_display() {
	if (archery.visible == WHITE && settingsetup.num_arrow != 0) {
		view_render.drawBitmap(	archery.x, \
								archery.y - 10, \
								bitmap_archery_I, \
								SIZE_BITMAP_ARCHERY_X, \
								SIZE_BITMAP_ARCHERY_Y, \
								WHITE);
	}
	else if (archery.visible == WHITE && settingsetup.num_arrow == 0) {
		view_render.drawBitmap(	archery.x, \
								archery.y - 10, \
								bitmap_archery_II, \
								SIZE_BITMAP_ARCHERY_X, \
								SIZE_BITMAP_ARCHERY_Y, \
								WHITE);
	}
}
```

**Arrow display:**
```sh
void ar_game_arrow_display() {
	for (uint8_t i = 0; i < MAX_NUM_ARROW; i++) {
		if (arrow[i].visible == WHITE) {
			view_render.drawBitmap(	arrow[i].x, \
									arrow[i].y, \
									bitmap_arrow, \
									SIZE_BITMAP_ARROW_X, \
									SIZE_BITMAP_ARROW_Y, \
									WHITE);
		}
	}
}
```

**Meteoroid display:**
```sh
void ar_game_meteoroid_display() {
	for (uint8_t i = 0; i < NUM_METEOROIDS; i++) {
		if (meteoroid[i].visible == WHITE) {
			if (meteoroid[i].action_image == 1) {
				view_render.drawBitmap(	meteoroid[i].x, \
										meteoroid[i].y, \
										bitmap_meteoroid_I, \
										SIZE_BITMAP_METEOROIDS_X, \
										SIZE_BITMAP_METEOROIDS_Y, \
				 						WHITE);
			}
			else if (meteoroid[i].action_image == 2) {
				view_render.drawBitmap(	meteoroid[i].x, \
										meteoroid[i].y, \
										bitmap_meteoroid_II, \
										SIZE_BITMAP_METEOROIDS_X, \
										SIZE_BITMAP_METEOROIDS_Y, \
										WHITE);
			}
			else if (meteoroid[i].action_image == 3) {
				view_render.drawBitmap(	meteoroid[i].x, \
										meteoroid[i].y, \
										bitmap_meteoroid_III, \
										SIZE_BITMAP_METEOROIDS_X, \
										SIZE_BITMAP_METEOROIDS_Y, \
				 						WHITE);
			}
		}
	}
}
```

**Bang display:**
```sh
void ar_game_bang_display() {
	for (uint8_t i = 0; i < NUM_BANG; i++) {
		if (bang[i].visible == WHITE) {
			if (bang[i].action_image == 1) {
				view_render.drawBitmap(	bang[i].x, \
										bang[i].y, \
										bitmap_bang_I, \
										SIZE_BITMAP_BANG_I_X, \
										SIZE_BITMAP_BANG_I_Y, \
										WHITE);
			}
			else if (bang[i].action_image == 2) {
				view_render.drawBitmap(	bang[i].x, \
										bang[i].y, \
										bitmap_bang_II, \
										SIZE_BITMAP_BANG_I_X, \
										SIZE_BITMAP_BANG_I_Y, \
				 						WHITE);
			}
			else if (bang[i].action_image == 3) {
				view_render.drawBitmap( bang[i].x + 2, \
										bang[i].y - 1, \
										bitmap_bang_III, \
										SIZE_BITMAP_BANG_II_X, \
										SIZE_BITMAP_BANG_II_Y, \
				 						WHITE);
			}
		}
	}
}
```

**Border display:**
```sh
void ar_game_border_display() {
	if (border.visible == WHITE) {
		view_render.drawFastVLine(	border.x, \
									AXIS_Y_BORDER_ON, \
									AXIS_Y_BORDER_UNDER, \
									WHITE);
		for (uint8_t i = 0; i < NUM_METEOROIDS; i++) {
			view_render.fillCircle(	border.x, \
									meteoroid[i].y + 5, \
									1, \
									WHITE);
		}
	}
}
```

**Game frame display:**
```sh
void ar_game_frame_display() {
	view_render.setTextSize(1);
	view_render.setTextColor(WHITE);
	view_render.setCursor(2,55);
	view_render.print("Arrow:");
	view_render.print(settingsetup.num_arrow);
	view_render.setCursor(60,55);
	view_render.print(" Score:");
	view_render.print(ar_game_score);
	view_render.drawLine(0, LCD_HEIGHT, 	LCD_WIDTH, LCD_HEIGHT,		WHITE);
	view_render.drawLine(0, LCD_HEIGHT-10, 	LCD_WIDTH, LCD_HEIGHT-10,	WHITE);
	view_render.drawRect(0, 0, 128, 64, 1);
}
```

**Screen display:**
```sh
void view_scr_archery_game() {
	if (ar_game_status == GAME_ON) {
		ar_game_frame_display();
		ar_game_archery_display();
		ar_game_arrow_display();
		ar_game_meteoroid_display();
		ar_game_bang_display();
		ar_game_border_display();
	}
	else if (ar_game_status == GAME_OVER) {
		view_render.clear();
		view_render.setTextSize(2);
		view_render.setTextColor(WHITE);
		view_render.setCursor(17, 24);
		view_render.print("YOU LOSE");
	}
}
```

</details>

### 4.3 Âm thanh
Âm thành được thiết kế qua wed [Arduino Music](https://www.instructables.com/Arduino-Music-From-Sheet-Music/)

Trong trò chơi, để trò chơi thêm phần xin động thì việc có âm thanh là điều cần thiết. 

Các âm thanh cần thiết kế: nút nhấn, bắn tên, vụ nổ, nhạc game.

**Code:**
<details>

```sh
// Âm thanh Bắt đầu game 
BUZZER_PlayTones(tones_SMB);

// Âm thanh Vụ nổ 
BUZZER_PlayTones(tones_BUM);

// Âm thanh nút nhấn
BUZZER_PlayTones(tones_cc);

// Âm thanh cảnh báo
BUZZER_PlayTones(tones_3beep);

// Merry Christmas
BUZZER_PlayTones(tones_merryChrismast);

/*________________BUZZER______________*/

void BUZZER_Sleep(bool sleep);
/*  sleep = 0 : bật âm thanh 
    sleep = 1 : tắt âm thanh */
static const Tone_TypeDef tones_BUM[] = {
	{3000,3},
	{4500,6},
	{   0,0}
};

static const Tone_TypeDef tones_cc[] = {
	{2000,2}, 
	{   0,0}, 
};

static const Tone_TypeDef tones_startup[] = {
	{2000,3},
	{   0,3},
	{3000,3},
	{   0,3},
	{4000,3},
	{   0,3},
	{1200,4},
	{   0,6},
	{4500,6},
	{   0,0}     // <-- tones end
};

static const Tone_TypeDef tones_3beep[] = {
	{4000, 3},
	{   0,10},
	{1000, 6},
	{   0,10},
	{4000, 3},
	{   0, 0}
};

// "Super Mario bros." =
static const Tone_TypeDef tones_SMB[] = {
	{2637,18}, // E7 x2
	{   0, 9}, // x3
	{2637, 9}, // E7
	{   0, 9}, // x3
	{2093, 9}, // C7
	{2637, 9}, // E7
	{   0, 9}, // x3
	{3136, 9}, // G7
	{   0,27}, // x3
	{1586, 9}, // G6
	{   0,27}, // x3

	{2093, 9}, // C7
	{   0,18}, // x2
	{1586, 9}, // G6
	{   0,18}, // x2
	{1319, 9}, // E6
	{   0,18}, // x2
	{1760, 9}, // A6
	{   0, 9}, // x1
	{1976, 9}, // B6
	{   0, 9}, // x1
	{1865, 9}, // AS6
	{1760, 9}, // A6
	{   0, 9}, // x1

	{1586,12}, // G6
	{2637,12}, // E7
	{3136,12}, // G7
	{3520, 9}, // A7
	{   0, 9}, // x1
	{2794, 9}, // F7
	{3136, 9}, // G7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2093, 9}, // C7
	{2349, 9}, // D7
	{1976, 9}, // B6
	{   0,18}, // x2

	{2093, 9}, // C7
	{   0,18}, // x2
	{1586, 9}, // G6
	{   0,18}, // x2
	{1319, 9}, // E6
	{   0,18}, // x2
	{1760, 9}, // A6
	{   0, 9}, // x1
	{1976, 9}, // B6
	{   0, 9}, // x1
	{1865, 9}, // AS6
	{1760, 9}, // A6
	{   0, 9}, // x1

	{1586,12}, // G6
	{2637,12}, // E7
	{3136,12}, // G7
	{3520, 9}, // A7
	{   0, 9}, // x1
	{2794, 9}, // F7
	{3136, 9}, // G7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2093, 9}, // C7
	{2349, 9}, // D7
	{1976, 9}, // B6

	{   0, 0}
};

// Merry Christmas
static const Tone_TypeDef tones_merryChrismast[] = {
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0,18}, // x2

	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0,18}, // x2

	{2637, 9}, // E7
	{   0, 9}, // x1
	{3136, 9}, // G7
	{   0, 9}, // x1
	{2093, 9}, // C7
	{   0, 9}, // x1
	{2349, 9}, // D7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0,24}, // x2

	{2794, 9}, // F7
	{   0, 9}, // x1
	{2794, 9}, // F7
	{   0, 9}, // x1
	{2794, 9}, // F7
	{   0, 9}, // x1
	{2794, 9}, // F7
	{   0, 9}, // x1
	{2794, 9}, // F7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2349, 9}, // D7
	{   0, 9}, // x1
	{2349, 9}, // D7
	{   0, 9}, // x1
	{2637, 9}, // E7
	{   0, 9}, // x1
	{2349, 9}, // D7
	{   0, 9}, // x1
	{3136, 9}, // G7
	{   0, 0}  // <-- tones end
};
```

</details>

**Ghi chú:** Nêu không có thời gian hay không có kiếu âm nhạc thì tốt nhất nên dùng các thư viện trên github
