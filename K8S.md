# K8S部署说明
## 1，如何访问外部数据库
k8s中定义无选择器的Service，定义外部EndPoints
例：
（以下配置需要k8s运维协助部署）
```
apiVersion: v1
kind: Service
metadata:
  name: boolean-mysql
spec:
  ports:
    - protocol: TCP
      port: 3306  #本地app使用的端口，service使用的端口
      targetPort: 3307 #外部真实服务的端口，容器的端口，或者说service转发的端口
--
apiVersion: v1
kind: Endpoints
metadata:
  name: boolean-mysql
subsets:
  - addresses:
      - ip: 192.0.2.42 #外部真实数据库地址
    ports:
      - port: 3307  #外部真实数据库端口
```
SpringBoot打包时，定义的数据库地址这样写：
jdbc:mysql://boolean-mysql:3306/test?user=xxx&password=xxx
端口使用3306，真实端口在上面配置文件里面映射了，此例为3307
mysql-service就是上面定义的service名称

## 2，springboot的k8s部署文件
例：
```
kind: Service
apiVersion: v1
metadata:
  name: boolean-springboot
spec:
  type: NodePort #可以通过节点名称访问服务
  selector:
    app: boolean-springboot-app
  ports:
    - protocol: TCP
      port: 8080 #service的端口，vue访问用这个
      targetPort: 8080 #springboot服务的端口
      nodePort: 32082 #外部访问的端口，后端一般不用暴露，测试用

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: boolean-springboot-deployment
spec:
  replicas: 1 #副本数量
  selector:
    matchLabels:
      app: boolean-spring-boot-app
  template:
    metadata:
      labels:
        app: boolean-spring-boot-app
    spec:
      containers:
        - name: boolean-spring-boot-app-controller
          image: 镜像名称，用打包导入的镜像名称，比如：boolean-img:v1
          ports:
            - containerPort: 8080
```
和上面那个mysql的配置结合起来使用，现部署mysql的，再部署springboot的

最后用 http://node的ip地址:32082来访问

## 3，nginx+vue的k8s部署文件
```
kind: Service
apiVersion: v1
metadata:
  name: boolean-vue
spec:
  type: NodePort #可以通过节点名称访问服务
  selector:
    app: boolean-springboot-app
  ports:
    - protocol: TCP
      port: 80 #service的端口
      targetPort: 80 #nginx服务监听的端口
      nodePort: 32083 #外部访问的端口，后端一般不用暴露，测试用

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: boolean-vue-deployment
spec:
  replicas: 1 #副本数量
  selector:
    matchLabels:
      app: boolean-vue-app
  template:
    metadata:
      labels:
        app: boolean-vue-app
    spec:
      containers:
        - name: boolean-vue-app-controller
          image: 镜像名称，用打包导入的镜像名称，比如：boolean-img:v1
          ports:
            - containerPort: 80
```
vue打包的时候，服务的的地址这么配置 http://boolean-springboot:8080/后面跟你的路径

最后用http://nodeip:32083/xxx来访问前端服务

nodeip找运维要
