<strong>Lập trình ESP32 MQTT bật tắt đèn với Hivemq Broker</strong>
**1, Khái niệm PubSub**
PubSub là một mô hình giao tiếp trong lập trình phần mềm, nơi các thành phần gửi và nhận thông điệp thông qua việc xuất bản và đăng ký các thông điệp trên các chủ đề (topics) chung. Người xuất bản tạo và gửi thông điệp vào chủ đề, trong khi người đăng ký quan tâm đến các thông điệp trong chủ đề đó và nhận thông điệp khi chúng được gửi đi. Mô hình này được sử dụng trong các hệ thống phân tán và cung cấp tính linh hoạt và khả năng mở rộng. Một ví dụ thực tế của PubSub là hệ thống gửi thông báo trong ứng dụng di động.

**2, Mô hình client/server**
Mô hình Server/Client là một kiến trúc phần mềm phổ biến trong lập trình mạng, trong đó có sự tương tác giữa các máy chủ (server) và các máy khách (client).

Trong mô hình này, máy chủ là một hệ thống hoặc dịch vụ cung cấp các tài nguyên và dịch vụ. Nó lắng nghe và đáp ứng yêu cầu từ các máy khách. Máy khách là các thiết bị hoặc ứng dụng yêu cầu tài nguyên hoặc dịch vụ từ máy chủ.

Quá trình hoạt động của mô hình Server/Client diễn ra như sau: máy khách gửi yêu cầu đến máy chủ thông qua giao thức mạng như HTTP. Máy chủ nhận yêu cầu, xử lý nó và trả về kết quả cho máy khách. Máy khách sau đó sử dụng kết quả để thực hiện các tác vụ tiếp theo.

Mô hình Server/Client thường được sử dụng trong các ứng dụng web, nơi máy chủ web cung cấp nội dung và dịch vụ cho các trình duyệt web trên các máy khách. Máy chủ có thể cung cấp các tài nguyên như file, trang web, dữ liệu và các dịch vụ như đăng nhập, thanh toán, xử lý dữ liệu, và nhiều hơn nữa.

Mô hình Server/Client cho phép phân chia công việc và trách nhiệm giữa máy chủ và máy khách. Máy chủ đảm nhận vai trò chính trong việc cung cấp dịch vụ và xử lý logic, trong khi máy khách tận dụng các tài nguyên và dịch vụ được cung cấp bởi máy chủ.

**3, Sự khác nhau:**
PubSub không phải là một tương tác giữa Server và Client trong kiến trúc Server/Client truyền thống. Thay vào đó, PubSub là một mô hình giao tiếp giữa các thành phần hoặc hệ thống khác nhau trong lập trình phần mềm.

Trong mô hình PubSub, có hai thành phần chính: người xuất bản (publisher) và người đăng ký (subscriber). Người xuất bản tạo ra thông điệp và gửi chúng đến các chủ đề (topics) chung. Người đăng ký quan tâm đến các chủ đề cụ thể và đăng ký để nhận thông điệp từ chúng.

Khác với mô hình Server/Client, PubSub không yêu cầu sự tương tác trực tiếp giữa Server và Client. Thay vào đó, thông điệp được xuất bản vào chủ đề và gửi đến tất cả các người đăng ký đã đăng ký cho chủ đề đó. Quá trình này không đồng bộ và thông qua một cơ chế trung gian, thường là một hệ thống hoặc dịch vụ PubSub.

Mô hình PubSub thường được sử dụng trong các hệ thống phân tán, nơi các thành phần cần giao tiếp với nhau mà không cần biết đến sự tồn tại cụ thể của nhau. Nó cung cấp tính linh hoạt và khả năng mở rộng, cho phép các thành phần mới tham gia hoặc rời khỏi hệ thống một cách dễ dàng mà không ảnh hưởng đến các thành phần khác.

**4, Khái niệm MQTT**
MQTT (Message Queuing Telemetry Transport) là một giao thức gửi nhận thông điệp đơn giản, nhẹ nhàng và được sử dụng rộng rãi trong các ứng dụng IoT (Internet of Things) và M2M (Machine-to-Machine) communication.

MQTT được thiết kế để hoạt động trên các mạng có băng thông hạn chế, kết nối không đáng tin cậy và thiết bị có tài nguyên thấp như cảm biến và thiết bị nhúng. Giao thức này sử dụng mô hình giao tiếp Publish-Subscribe (PubSub) mà tôi đã giải thích trước đó.

Trong MQTT, có ba thành phần chính:

Máy chủ MQTT (MQTT broker): Là trung tâm của hệ thống MQTT, nơi các thiết bị MQTT kết nối và gửi/nhận thông điệp. Máy chủ MQTT nhận thông điệp từ nguồn xuất bản (publisher) và chuyển tiếp nó đến các thiết bị đã đăng ký nhận thông điệp (subscriber).

Người xuất bản MQTT (MQTT publisher): Là một thiết bị hoặc ứng dụng gửi thông điệp đến máy chủ MQTT. Người xuất bản chỉ cần đăng nhập vào máy chủ MQTT và gửi thông điệp đến một chủ đề (topic) cụ thể.

Người đăng ký MQTT (MQTT subscriber): Là một thiết bị hoặc ứng dụng quan tâm và đăng ký nhận thông điệp từ máy chủ MQTT. Người đăng ký chỉ cần đăng nhập vào máy chủ MQTT và đăng ký nhận thông điệp từ một hoặc nhiều chủ đề.

MQTT sử dụng mô hình giao tiếp bất đồng bộ (asynchronous) và có độ trễ thấp. Nó cho phép các thiết bị gửi và nhận thông điệp theo yêu cầu mà không cần thiết lập kết nối liên tục. Giao thức này cũng hỗ trợ cơ chế QoS (Quality of Service) để đảm bảo giao tiếp tin cậy và có khả năng xác nhận giao nhận thông điệp.

MQTT là một giao thức linh hoạt và dễ triển khai, được sử dụng rộng rãi trong các ứng dụng IoT như kiểm soát thiết bị thông minh, giám sát môi trường, hệ thống nhà thông minh và nhiều ứng dụng khác.
Ngoài ra quan tâm: AMQP, CoAP, DDS, XMPP

**5, Dùng hivemq**
[Trang web] [https://www.hivemq.com/mqtt/public-mqtt-broker/] cung cấp thông tin về MQTT và cung cấp một công cụ Public MQTT Broker miễn phí bởi HiveMQ. Dưới đây là một số thông tin cơ bản về MQTT được đề cập trên trang web này:

MQTT Essentials: Trang web cung cấp các tài liệu MQTT Essentials để bạn nắm vững kiến thức cơ bản về giao thức MQTT. Bạn có thể tìm hiểu về các khái niệm quan trọng như publish-subscribe, QoS (Quality of Service), retained messages và nhiều hơn nữa.

MQTT FAQs: Trang web cung cấp một danh sách các câu hỏi thường gặp về MQTT và cung cấp câu trả lời chi tiết cho mỗi câu hỏi. Điều này giúp bạn hiểu rõ hơn về giao thức MQTT và cách sử dụng nó trong ứng dụng IoT của bạn.

Public MQTT Broker: HiveMQ cung cấp một Public MQTT Broker miễn phí mà bất kỳ ai cũng có thể sử dụng. Bạn có thể viết một MQTT client kết nối với broker này. Trang web cung cấp địa chỉ và cổng kết nối cho MQTT broker, bao gồm cả các cổng bảo mật TLS và cổng Websocket.

MQTT Client Libraries: HiveMQ cung cấp một danh sách các thư viện MQTT client khác nhau mà bạn có thể sử dụng để kết nối với HiveMQ MQTT broker. Các thư viện này hỗ trợ nhiều ngôn ngữ lập trình khác nhau và giúp bạn dễ dàng triển khai MQTT trong ứng dụng của mình.

MQTT Browser Client: HiveMQ cung cấp MQTT Browser Client, một giao diện MQTT WebSocket client cho phép bạn sử dụng MQTT trực tiếp từ trình duyệt web mà không cần tải xuống và cài đặt phần mềm. Điều này cho phép bạn sử dụng MQTT trên bất kỳ thiết bị nào và hỗ trợ đầy đủ cho giao thức MQTT.

Nếu bạn quan tâm đến chi tiết hơn, tôi khuyến nghị truy cập trang web https://www.hivemq.com/mqtt/public-mqtt-broker/ để tìm hiểu thêm và khám phá các tài nguyên MQTT mà HiveMQ cung cấp.

**6, Cài đặt MQTT trên ESP32**
Để cài đặt thư viện MQTT client trên ESP32, bạn có thể sử dụng thư viện "PubSubClient":
Mở Arduino IDE.
Nhấp vào menu "Sketch" và chọn "Include Library" > "Manage Libraries...".
Trong hộp tìm kiếm, nhập "PubSubClient" và nhấp Enter.
Tìm thư viện "PubSubClient" trong kết quả tìm kiếm và nhấp vào nút "Install" để cài đặt thư viện.
Chờ đợi quá trình cài đặt hoàn thành.
Sau khi thư viện PubSubClient đã được cài đặt thành công, bạn có thể sử dụng nó để phát triển ứng dụng MQTT client trên ESP32. Bạn có thể tham khảo các ví dụ và tài liệu hướng dẫn trên trang web chính thức của thư viện PubSubClient để biết cách sử dụng thư viện này trong việc kết nối và giao tiếp với MQTT broker.

Dùng MQTT online:Lập trình ESP32 MQTT bật tắt đèn với Hivemq Broker
- Khái niệm:
Hivemq Broker chia thành 2 loại:
Public broker: Sử dụng cổng 1883, không có bảo mật, thường dùng để test ứng dụng hoặc các sản phẩm đơn giản
Private broker: Sử dụng cổng 8883 và bảo mật SSL/TLS. Bạn có thể sử dụng nó trong các sản phẩm thương mại, nhưng tất nhiên nên để ý các điều khoản khi sử dụng nhé
- Cách sử dụng Public broker
Link ứng dụng: https://www.hivemq.com/
Link ảnh: https://drive.google.com/drive/folders/14kIu2sUUVLoCfeHMQGtzChkAY9ea1KHR
- Lập trình ESP32 MQTT
+ Cài đặt thư viện pubsubclient



