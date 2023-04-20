# k8s 挂载init容器
initcontainer的方式，把自动化测试脚本挂载到httprunner的目录
InitContainer 是 Kubernetes 中的一个特殊容器，可以在主容器启动之前运行。可以使用 InitContainer 来完成一些初始化任务，例如配置文件的注入、数据预处理等。

要将自动化测试脚本挂载到 Httprunner 的目录，可以使用 InitContainer 容器完成此任务。下面是一个使用 InitContainer 容器将自动化测试脚本挂载到 Httprunner 目录的示例 YAML 文件：

```yaml
Copy code
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: main-container
    image: httprunner:latest
    command: ["python", "main.py"]
    volumeMounts:
    - name: test-scripts
      mountPath: /app/tests
  initContainers:
  - name: init-test-scripts
    image: busybox
    command: ['sh', '-c', 'cp /mnt/test-scripts/* /app/tests']
    volumeMounts:
    - name: test-scripts
      mountPath: /mnt/test-scripts
  volumes:
  - name: test-scripts
    configMap:
      name: test-scripts
      items:
      - key: test.py
        path: test.py
```
在这个示例中，我们创建了一个名为 test-pod 的 Pod，其中包含了一个名为 main-container 的主容器和一个名为 init-test-scripts 的 InitContainer 容器。main-container 容器运行 httprunner 镜像，并挂载了一个名为 test-scripts 的卷，将其中的文件挂载到容器中的 /app/tests 目录下。

InitContainer 容器使用 busybox 镜像，将 /mnt/test-scripts 目录下的所有文件复制到容器中的 /app/tests 目录下，从而将自动化测试脚本挂载到 Httprunner 的目录中。test-scripts 卷使用了 ConfigMap 对象来存储测试脚本文件，并在 Pod 中被引用。


如果自动化测试脚本已经打包成镜像了，那么可以直接在 Kubernetes 中使用该镜像运行测试任务。以下是一个示例 YAML 文件：
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: test-container
    image: test-image:latest
    command: ["python", "test.py"]
```
在这个示例中，我们创建了一个名为 test-pod 的 Pod，其中包含了一个名为 test-container 的容器。该容器使用了 test-image:latest 镜像，并运行了 python test.py 命令，来执行测试任务。

需要注意的是，如果测试脚本需要访问外部资源，例如数据库或者 API 接口等，需要在容器的启动参数中设置相应的环境变量或者配置文件，以便测试脚本能够正确地访问这些资源。

另外，如果测试任务需要使用多个容器，例如一个容器用于运行测试脚本，另一个容器用于提供测试所需的服务，可以使用 Kubernetes 的 Deployment 对象来创建一个具有多个容器的 Pod。