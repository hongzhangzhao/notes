


# 1 角色

- CAS Client  用户
- CAS Server  中心服务器
- CAS Service (各个服务)


# 2 Token类型

- TGT: 用户在Server登录成功后, 生成的凭据, 会保存到session中
- TGC: 类似sessionid, 用来唯一标识TGT
- ST: 用户要想访问Service, 需要拿着TGC向Server获取ST, 用户得到ST后访问Service, Service会向Server验证ST. 验证成功就会返回资源.


# 3 流程


[[CAS架构图.excalidraw]]


# 4 其他

- CAS只起到一半作用的产物, 它只能完成认证, 而不能授权.