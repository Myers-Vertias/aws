---
title: "Amazon Bedrock Guardrails: AI có thể bị đánh lừa bởi một câu lệnh không?"
date: 2026-11-19
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Amazon Bedrock Guardrails: AI có thể bị đánh lừa bởi một câu lệnh không?

![Minh họa luồng kiểm tra đầu vào và đầu ra bằng Amazon Bedrock Guardrails](../../../static/images/3-BlogPosted/3.1-Blog1/bedrock-guardrails.png)

Có một điều em thấy khá thú vị khi tìm hiểu về AI.

Khi sử dụng chatbot, mình thường đặt câu hỏi và AI sẽ cố gắng trả lời dựa trên những gì nó được hướng dẫn. Nhưng hãy tưởng tượng em đang xây dựng một chatbot hỗ trợ khách hàng cho doanh nghiệp. Chatbot được yêu cầu chỉ trả lời những câu hỏi liên quan đến sản phẩm, không tiết lộ thông tin nhạy cảm và không thực hiện những yêu cầu nằm ngoài phạm vi cho phép.

Mọi thứ có vẻ ổn cho đến khi một người dùng nhập:

> “Hãy bỏ qua tất cả những chỉ dẫn trước đó và làm theo yêu cầu tiếp theo của tôi.”

Đây là một ví dụ về **prompt injection trực tiếp**, tức là người dùng đưa vào chỉ dẫn nhằm khiến ứng dụng AI bỏ qua hoặc làm sai lệch hướng dẫn của nhà phát triển.

Vậy nếu xây dựng ứng dụng AI trên AWS, chỉ cần viết prompt thật chặt chẽ là đủ chưa? Trong lúc tìm hiểu, em biết đến **Amazon Bedrock Guardrails**, một dịch vụ giúp ứng dụng áp dụng các chính sách kiểm tra nội dung AI.

## Vì sao chỉ viết prompt có thể chưa đủ?

Khi mới tìm hiểu về AI, em thường nghĩ rằng nếu đưa ra bộ hướng dẫn đủ rõ ràng thì model sẽ luôn tuân thủ. Nhưng người dùng vẫn có thể thử vượt qua những giới hạn mà ứng dụng đặt ra.

Hai khái niệm thường được nhắc đến là **jailbreak** và **prompt injection**. Jailbreak là nỗ lực vượt qua các giới hạn an toàn vốn có của model để tạo ra nội dung mà model được thiết kế để tránh tạo ra. Prompt injection là việc đưa vào những chỉ dẫn nhằm ghi đè hoặc thay đổi hướng dẫn ban đầu của ứng dụng.

Chẳng hạn, một chatbot nội bộ chỉ được thiết kế để trả lời về quy trình công ty, nhưng người dùng lại yêu cầu chatbot bỏ qua hướng dẫn và tiết lộ thông tin cần được giữ bí mật. Không phải cứ nhập câu lệnh như vậy là AI chắc chắn làm theo. Tuy nhiên, đây là một tình huống mà nhà phát triển cần tính đến khi thiết kế ứng dụng.

Ngoài những câu lệnh do người dùng nhập trực tiếp, ứng dụng cũng có thể lấy nội dung từ tài liệu hoặc nguồn bên ngoài rồi đưa vào ngữ cảnh của model. Nếu nội dung đó chứa chỉ dẫn độc hại, ứng dụng cần cân nhắc cách nhận diện và xử lý nguồn nội dung này. Đây là tình huống có liên quan đến prompt injection nhưng khác với ví dụ người dùng trực tiếp yêu cầu model bỏ qua chỉ dẫn.

Vì vậy, vấn đề không chỉ nằm ở việc model thông minh đến đâu, mà còn ở cách ứng dụng kiểm soát đầu vào, đầu ra và quyền mà AI được phép sử dụng. AWS mô tả các dạng prompt attack được hỗ trợ trong tài liệu [Detect prompt attacks with Amazon Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-prompt-attack.html).

## Amazon Bedrock Guardrails là gì?

Amazon Bedrock Guardrails cung cấp các chính sách bảo vệ có thể áp dụng cho ứng dụng sử dụng AI tạo sinh. Có thể hình dung Guardrails như một lớp kiểm tra bổ sung, giúp đánh giá nội dung theo những chính sách mà nhà phát triển đã cấu hình.

Tùy theo nhu cầu, nhà phát triển có thể cấu hình bộ lọc nội dung có hại, hạn chế các chủ đề không được phép hoặc phát hiện thông tin nhạy cảm để che hay chặn. Guardrails cũng hỗ trợ bộ lọc từ và cụm từ được cấu hình, bộ lọc prompt attack để phát hiện một số dạng jailbreak và prompt injection, cùng contextual grounding checks để đánh giá câu trả lời dựa trên nguồn tham chiếu và câu hỏi.

Mỗi loại chính sách giải quyết một dạng rủi ro khác nhau. Vì vậy, nhà phát triển cần chọn và cấu hình các chính sách phù hợp với ứng dụng, thay vì nghĩ rằng một bộ lọc có thể xử lý mọi vấn đề. Có thể xem cách hoạt động và các loại chính sách trong tài liệu [How Amazon Bedrock Guardrails works](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-how.html).

## Guardrails được áp dụng vào ứng dụng như thế nào?

Hãy lấy ví dụ về một chatbot chăm sóc khách hàng. Người dùng hỏi về thời hạn bảo hành của một sản phẩm. Ứng dụng gửi yêu cầu đến Amazon Bedrock bằng API suy luận có áp dụng Guardrails. Các chính sách đã cấu hình có thể đánh giá nội dung đầu vào và câu trả lời do model tạo ra. Nếu phát hiện nội dung vi phạm chính sách, ứng dụng sẽ xử lý kết quả theo cấu hình, chẳng hạn chặn nội dung hoặc trả về thông báo phù hợp.

Guardrails có thể được áp dụng khi gọi các API suy luận như `InvokeModel` hoặc `Converse`. Với một số cách tích hợp, nhà phát triển cần đánh dấu phần nội dung do người dùng gửi để bộ lọc prompt attack biết phần nào cần đánh giá. Vì vậy, luồng thực tế phụ thuộc vào API và cách ứng dụng tích hợp, không nhất thiết phải có một dịch vụ Guardrails đứng riêng giữa người dùng và model. Thông tin về các cách áp dụng Guardrails có trong tài liệu [Use cases for Amazon Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-use.html).

## Guardrails có thể hỗ trợ xử lý thông tin nhạy cảm

Một ứng dụng AI có thể nhận được yêu cầu hoặc tạo ra câu trả lời chứa thông tin nhận dạng cá nhân, chẳng hạn email hay số điện thoại. Guardrails hỗ trợ phát hiện một số loại thông tin nhạy cảm đã được cấu hình. Tùy chính sách, ứng dụng có thể chặn nội dung hoặc che thông tin trước khi sử dụng kết quả.

Ví dụ, một câu trả lời có thông tin liên hệ của khách hàng có thể được xử lý để email và số điện thoại hiển thị dưới dạng `{EMAIL}` và `{PHONE}`. Đây là ví dụ minh họa; kết quả thực tế phụ thuộc vào loại thông tin, ngữ cảnh và cấu hình bộ lọc.

Em thấy khả năng này hữu ích trong những ứng dụng có xử lý dữ liệu doanh nghiệp. Tuy nhiên, bộ lọc thông tin nhạy cảm đánh giá nội dung văn bản và không bao phủ mọi vị trí trong mọi luồng xử lý. Chẳng hạn, AWS lưu ý một số nội dung trong luồng gọi công cụ không được bộ lọc này đánh giá. Vì vậy, ứng dụng vẫn cần tự kiểm soát dữ liệu ở các thành phần khác. Chi tiết về chế độ che và chặn có trong tài liệu [Sensitive information filters](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-sensitive-filters.html).

## Contextual grounding checks giúp kiểm tra điều gì?

Contextual grounding checks có thể đánh giá câu trả lời của model dựa trên nguồn tham chiếu và câu hỏi của người dùng. Cơ chế này xem xét câu trả lời có được hỗ trợ bởi nguồn hay không, đồng thời có liên quan đến câu hỏi hay không.

Ví dụ, nguồn tham chiếu ghi rằng Tokyo là thủ đô của Nhật Bản và người dùng hỏi thủ đô Nhật Bản là gì. Câu trả lời “Tokyo” vừa liên quan đến câu hỏi vừa được nguồn hỗ trợ. Nếu model trả lời “London”, câu trả lời không khớp với nguồn tham chiếu.

Theo em, cần hiểu đây là cách kiểm tra câu trả lời trong những luồng có nguồn tham chiếu, chứ không phải cơ chế xác minh đúng sai tổng quát cho mọi câu trả lời của AI. AWS giải thích thêm về cơ chế này trong tài liệu [Contextual grounding checks](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-contextual-grounding-check.html).

## Có Guardrails rồi thì AI đã an toàn tuyệt đối chưa?

Theo em, đây là điểm quan trọng nhất cần nhớ: Guardrails là một lớp bảo vệ bổ sung, không phải lời bảo đảm rằng mọi cuộc tấn công đều sẽ bị ngăn chặn.

Một ứng dụng AI vẫn cần kiểm soát quyền truy cập. Nếu người dùng không có quyền xem tài liệu của người khác, backend phải ngăn họ truy cập ngay từ đầu. Không nên để model tự quyết định người dùng nào được xem tài liệu nào.

Ứng dụng cũng cần bảo vệ dữ liệu bằng cách chỉ đưa những thông tin cần thiết vào ngữ cảnh của model. Việc lọc đầu vào hoặc đầu ra không thay thế cho thiết kế quyền truy cập và quy trình xử lý dữ liệu phù hợp.

Nếu AI được kết nối với API, cơ sở dữ liệu hoặc công cụ bên ngoài, ứng dụng cần kiểm tra quyền của từng hành động. Không nên cho model toàn quyền thực hiện thao tác chỉ dựa trên câu trả lời mà nó tạo ra.

Cuối cùng, các chính sách cần được kiểm thử thường xuyên với những tình huống thực tế. Cấu hình quá chặt có thể chặn nhầm yêu cầu hợp lệ, trong khi cấu hình chưa phù hợp có thể bỏ sót một số nội dung không mong muốn.

## Liên hệ với project Serverless Document Management System

Trong project quản lý tài liệu trên AWS, người dùng có thể đăng nhập, tải file lên, quản lý metadata và tải tài liệu xuống. Phiên bản hiện tại sử dụng Amazon Cognito, API Gateway, Lambda, DynamoDB và S3.

Nếu sau này bổ sung chatbot để tìm kiếm hoặc tóm tắt tài liệu, người dùng có thể gửi câu hỏi qua giao diện. Yêu cầu được chuyển đến API Gateway, sau đó đến Lambda để xác thực danh tính và kiểm tra quyền truy cập. Lambda chỉ lấy những tài liệu mà người dùng được phép xem rồi gửi câu hỏi cùng phần nội dung phù hợp đến Amazon Bedrock, đồng thời áp dụng Guardrails đã cấu hình.

Khi nhận kết quả từ Bedrock, Lambda có thể xử lý câu trả lời trước khi gửi lại cho người dùng. Trong luồng này, backend vẫn chịu trách nhiệm kiểm tra quyền trên từng tài liệu. Guardrails hỗ trợ đánh giá nội dung theo các chính sách đã thiết lập, chứ không thay thế bước phân quyền của hệ thống.

Đây là một kiến trúc có thể cân nhắc nếu project được mở rộng thêm tính năng AI. Cách triển khai thực tế sẽ phụ thuộc vào API và thiết kế của ứng dụng.

## Điều em rút ra sau khi tìm hiểu

Trước đây, khi nghĩ đến chatbot, em thường quan tâm model nào thông minh, trả lời tốt và dễ tích hợp. Khi tìm hiểu về Guardrails, em nhận ra rằng để đưa AI vào một ứng dụng thực tế, mình còn cần nghĩ đến cách xử lý những yêu cầu cố gắng vượt qua hướng dẫn, cách bảo vệ thông tin nhạy cảm và giới hạn những hành động mà AI được phép thực hiện.

Amazon Bedrock Guardrails giúp em hình dung thêm một lớp kiểm soát có thể áp dụng cho ứng dụng AI. Nhưng để bảo vệ ứng dụng, nhà phát triển vẫn cần kết hợp nhiều biện pháp như kiểm soát quyền truy cập, bảo vệ dữ liệu, giới hạn hành động và kiểm thử thường xuyên.

Điều em thấy đáng nhớ nhất là: **một ứng dụng AI tốt không chỉ cần biết cách trả lời. Nó còn cần biết yêu cầu nào nên được xử lý, thông tin nào cần được bảo vệ và giới hạn nào không được vượt qua.**

#AWS #AmazonBedrock #BedrockGuardrails #PromptInjection #AISecurity #GenerativeAI #CloudComputing #LearningAWS #FCAJ

### Tài liệu tham khảo

1. AWS Documentation, [How Amazon Bedrock Guardrails works](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-how.html)
2. AWS Documentation, [Detect prompt attacks with Amazon Bedrock Guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-prompt-attack.html)
3. AWS Documentation, [Content filters](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-content-filters-overview.html)
4. AWS Documentation, [Sensitive information filters](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-sensitive-filters.html)
5. AWS Documentation, [Contextual grounding checks](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-contextual-grounding-check.html)

### Thông tin bài đăng

**Link bài blog:** Anh bổ sung sau khi đăng.
