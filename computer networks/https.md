# HTTPS原理
* ## HTTPS加密步骤：
  1. ### client hello（客户端向服务器):
     1. 客户端支持的TLS版本
     2. 客户端支持的加密组件（Cipher Suites，内含支持的加密算法信息）
     3. 客户端生成的随机数
  2. ### server hello（服务器端向客户端发送）：
     1. 服务器端的TLS版本
     2. 服务器端从客户端支持的加密组件中挑选中的组件（Cipher Suite）
     3. 服务器端生成的随机数
  3. ### Certificate（服务器向客户端发送）：
     1. 服务器会向客户端发送其被CA签名的证书
  4. ### Server Key Exchange（服务器端向客户端发送）：
     1. 服务器向客户端发送密钥交换算法的参数（Server Params），服务器会用自己的私钥对Server Params进行签名，防止客户端收到的Server Params被伪造
  5. ### Server hello Done（服务器端向客户端发送）：
     1. 服务器端向客户端告知协商部分已经结束  
    ### (以上步骤客户端和服务器之间交换数据是明文的)
***
  6. ### CLient Key Exchange（客户端向服务端发送）：
     1. 在验证服务器证书可信后，客户端生成另一个密钥交换算法参数Client Params，根据之前服务端发送过来的Server Params，结合会生成一个随机数R3。由于之前客户端和服务器端都生成过自己的随机数，故而结合3个随机数会生成会话密钥。生成的会话密钥会通过服务器端公钥加密后发送给服务器端
  7. ### 之后客户端和服务器端之间的http通信都会通过会话密钥进行对称加密
