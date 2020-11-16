# -文件加密断点续传项目报告

1.	方案概述
本方案通过在传输时服务端按基因数据文件的文件名读取本地同名文件大小反馈给客户端，客户端从该点续传，从而实现断点续传功能。同时借助SSL对压缩后的基因数据进行加密传输来保证数据的安全性，实现基因数据的隐私保护。

2.	方案实现
项目采用Python3编写，主要使用socket, ssl库实现文件传输及加密功能。使用OpenSSL库生成证书和密钥文件。
（1）文件断点传输
Python中的socket（套接字）库可以实现客户端向服务端网络发出请求或应答请求，使两台主机之间可以进行通信。本方案利用socket库进行客户端服务端之间的网络连接建立，通过send()和recv()方法传输和接收文件。
断点续传功能实现：在客户端上传文件时会将文件名发送给服务端，服务端在接收文件之前搜索本地目录中是否已存在同名文件。若已存在，则读取文件大小并返回客户端，客户端从该大小值点续传文件；若不存在，则直接传输。
（2）文件加密传输
加密传输借助Python中的SSL库实现。
利用SSL实现加密传输，要求服务端首先产生自己的证书及公私钥，该步可通过OpenSSL工具实现。生成的证书文件需要传输给客户端，客户端根据证书中的公钥对传输数据进行加密，从而实现数据机密性的保护。

3.	传输性能测试
代码使用Python3.7.5编写，测试环境为客户端windows7 64bit，处理器Intel® Core™ i7-4720HQ CPU @ 2.60GHz，内存为8.00GB；服务端为VMware虚拟机环境下的ubuntu16.04 64位。
（1）公私钥及证书生成
使用第三方工具OpenSSL的req命令，按提示信息生成证书文件cert.pem和密钥文件key.pem。
 
图3.1	证书及私钥生成
将生成的证书文件cert.pem通过安全信道传输给客户端，其中包含生成的2048位RSA公钥。
（2）服务端在命令行界面运行put_server.py程序，主界面如图
 
图3.2	服务端界面
当提示“waiting…”时，表示已准备好接收文件。（图中出现的OSError是由于上次程序运行结束后地址监听未立即解除，此时稍等几分钟再试即可）。
（3）客户端执行put_client.py，主界面如图
 
图3.3	客户端主界面
当出现“欢迎登录”字样时表示已建立到客户端的套接字连接，此时可以指定本地路径下的文件进行传输。
（4）基因数据文件使格式为fq.bin.gtz的二进制文件，这里选择一个大小为780MB的文件进行测试
 
图3.4	测试文件属性
在客户端命令行中按提示格式输入该文件路径及目标服务端目录，开始传输。在传输到71%时手动中断传输
 
图3.5	手动中断传输
此时查看目标路径，可知已传输559MB文件
 
图3.6	已传输文件信息
此时客户端再次启动传输，步骤与之前相同，此时提示“文件已存在，是否续传？”，选择“Y”进行续传（此时若输入“N”，则是从头开始重新上传，而不是取消上传），如图
 
图3.7	选择续传文件








上传成功后提示“文件上传成功”，如图
 
 
图3.8	客户端和服务端提示信息
（5）验证文件的完整性，利用文件的SHA1值进行验证，结果如图，可知文件在传输过程中未出现错误
 
图3.9	客户端文件SHA1值
 
图3.10	服务端接收文件SHA1值

传输整个文件耗时约50秒（受网络状况等因素影响，实际网络环境下可能会有波动）。

