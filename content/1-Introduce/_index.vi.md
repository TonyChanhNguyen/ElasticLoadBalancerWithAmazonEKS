---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

Trước khi tiến hành phần thực hành, dưới đây là tổng quan 2 loại dịch vụ mà chúng ta sẽ sử dụng và tích hợp với EKS:

+ [Amazon Elastic Block Store](https://aws.amazon.com/ebs/) (chỉ hổ trợ EC2): dịch vụ lưu trữ khối cung cấp quyền truy cập trực tiếp từ các phiên bản và bộ chứa EC2 vào ổ lưu trữ chuyên dụng được thiết kế cho cả khối lượng công việc thông lượng và khối lượng giao dịch chuyên sâu ở mọi quy mô.
+ [Amazon Elastic File System](https://aws.amazon.com/efs/) (hổ trợ Fargate và EC2): một hệ thống tệp linh hoạt, có thể mở rộng và được quản lý đầy đủ, rất phù hợp để phân tích dữ liệu lớn, phân phối web và quản lý nội dung, phát triển và thử nghiệm ứng dụng, quy trình làm việc về phương tiện và giải trí, sao lưu cơ sở dữ liệu và lưu trữ vùng chứa. EFS lưu trữ dữ liệu của bạn một cách dư thừa trên nhiều Vùng sẵn sàng (AZ) và cung cấp quyền truy cập có độ trễ thấp từ các nhóm Kubernetes bất kể AZ mà chúng đang chạy trong đó.

**Hai loai dịch vụ dưới đây không nằm trong phạm vi của bài thực hành này.**

+ [Amazon FSx for NetApp ONTAP](https://aws.amazon.com/fsx/netapp-ontap/) (chỉ hổ trợ EC2): Bộ nhớ chia sẻ được quản lý hoàn toàn được xây dựng trên hệ thống tệp ONTAP phổ biến của NetApp. FSx cho NetApp ONTAP lưu trữ dữ liệu của bạn một cách dự phòng trên nhiều Vùng sẵn sàng (AZ) và cung cấp quyền truy cập có độ trễ thấp từ các nhóm Kubernetes bất kể AZ mà chúng đang chạy trong đó.
+ [FSx for Lustre](https://aws.amazon.com/fsx/lustre/) (chỉ hổ trợ EC2): một hệ thống tệp hiệu suất cao, được quản lý hoàn toàn, được tối ưu hóa cho các khối lượng công việc như học máy, điện toán hiệu suất cao, xử lý video, lập mô hình tài chính, tự động hóa thiết kế điện tử và phân tích. Với FSx for Lustre, bạn có thể nhanh chóng tạo một hệ thống tệp hiệu suất cao được liên kết với kho lưu trữ dữ liệu S3 của mình và truy cập một cách minh bạch các đối tượng S3 dưới dạng tệp.
+ [Amazon Simple Storage Service](https://aws.amazon.com/s3) (chỉ hổ trợ EC2): là một dịch vụ lưu trữ đối tượng cung cấp khả năng mở rộng, tính khả dụng của dữ liệu, bảo mật và hiệu suất hàng đầu trong ngành. Khách hàng thuộc mọi quy mô và ngành nghề có thể lưu trữ và bảo vệ bất kỳ lượng dữ liệu nào cho hầu hết mọi trường hợp sử dụng, chẳng hạn như hồ dữ liệu, ứng dụng gốc trên nền tảng đám mây và ứng dụng di động. Với các lớp lưu trữ tiết kiệm chi phí và các tính năng quản lý dễ sử dụng, bạn có thể tối ưu hóa chi phí, sắp xếp dữ liệu và định cấu hình các biện pháp kiểm soát truy cập tinh chỉnh để đáp ứng các yêu cầu cụ thể về kinh doanh, tổ chức và tuân thủ.

Việc làm quen với một số khái niệm về [Bộ lưu trữ Kubernetes](https://kubernetes.io/docs/concepts/storage/) cũng rất quan trọng:

+ [Volumes](): Các tệp trên đĩa trong vùng chứa là tạm thời, điều này gây ra một số vấn đề đối với các ứng dụng không tầm thường khi chạy trong vùng chứa. Một vấn đề là mất tập tin khi vùng chứa gặp sự cố. Kubelet khởi động lại container nhưng ở trạng thái sạch. Sự cố thứ hai xảy ra khi chia sẻ tệp giữa các vùng chứa chạy cùng nhau trong Pod. Sự trừu tượng hóa khối lượng Kubernetes giải quyết cả hai vấn đề này. Nên làm quen với Pods.
+ [Ephemeral Volumes]() được thiết kế cho những trường hợp sử dụng này. Vì các tập tuân theo vòng đời của Pod và được tạo và xóa cùng với Pod, nên các Pod có thể bị dừng và khởi động lại mà không bị giới hạn ở nơi có sẵn một số ổ đĩa cố định.
+ [Persistent Volumes (PV)]() là một phần lưu trữ trong cụm được quản trị viên cấp phép hoặc được cấp phép động bằng Lớp lưu trữ. Đó là tài nguyên trong cụm giống như nút là tài nguyên cụm. PV là các plugin âm lượng giống như Volume, nhưng có vòng đời độc lập với bất kỳ Pod riêng lẻ nào sử dụng PV. Đối tượng API này nắm bắt các chi tiết về việc triển khai bộ lưu trữ, có thể là NFS, iSCSI hoặc hệ thống lưu trữ dành riêng cho nhà cung cấp đám mây.
+ [Persistent Volume Claim (PVC)]() là yêu cầu lưu trữ của người dùng. Nó tương tự như Pod. Các nhóm tiêu thụ tài nguyên nút và PVC tiêu thụ tài nguyên PV. Các nhóm có thể yêu cầu các mức tài nguyên cụ thể (CPU và Bộ nhớ). Các xác nhận quyền sở hữu có thể yêu cầu kích thước và chế độ truy cập cụ thể (ví dụ: chúng có thể được gắn ReadWriteOnce, ReadOnlyMany hoặc ReadWriteMany.
+ [Storage Classes]()
cung cấp một cách để quản trị viên mô tả "loại" bộ nhớ mà họ cung cấp. Các lớp khác nhau có thể ánh xạ tới các mức chất lượng dịch vụ hoặc tới các chính sách dự phòng hoặc tới các chính sách tùy ý được xác định bởi quản trị viên cụm. Bản thân Kubernetes không có ý kiến ​​gì về những gì các lớp đại diện. Khái niệm này đôi khi được gọi là "hồ sơ" trong các hệ thống lưu trữ khác.
+ [Dynamic Volume]() Việc cung cấp cho phép khối lượng lưu trữ được tạo theo yêu cầu. Nếu không cung cấp động, quản trị viên cụm phải thực hiện lệnh gọi thủ công đến nhà cung cấp dịch vụ lưu trữ hoặc đám mây của họ để tạo khối lượng lưu trữ mới, sau đó tạo các đối tượng PersistentVolume để thể hiện chúng trong Kubernetes. Tính năng cung cấp động giúp loại bỏ nhu cầu quản trị viên cụm phải cung cấp trước bộ nhớ. Thay vào đó, nó tự động cung cấp dung lượng lưu trữ khi người dùng yêu cầu.

**Trong hội thảo này, chúng tôi sẽ chỉ tập trung vào cách tích hợp bộ lưu trữ cố định trên Cụm EKS với Amazon EBS và Amazon EFS bằng Cung cấp tĩnh và Cung cấp động.**
