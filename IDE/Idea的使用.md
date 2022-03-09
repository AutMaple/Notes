# IDEA 常用快捷键

代码编辑相关的快捷键

| 快捷键          | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| Alt + Enter     | 提示修改错误的方法                                           |
| Alt + Insert    | 插入构造方法，getter, setter等                               |
| Alt + Shift + k | 自动补全方法的返回值和类型。也可以使用（new User().var 然后按下 Tab 键，IDEA 也会补全类型 User user） |
| Ctrl + O        | 填充父类和接口的方法                                         |
| Ctrl + Alt + m  | 将选中的区域变成一个方法                                     |

查看代码相关的快捷键

| 快捷键         | 作用                         |
| -------------- | ---------------------------- |
| Ctrl + h       | 显示类或接口的继承链和实现类 |
| Ctrl + F12     | 显示类的所有方法和属性       |
| Ctrl + Alt + U | 查看类的继承关系图           |
| Ctrl  + b      | 直接跳转到类的定义处         |
|                |                              |

### idea 激活

公众号：Tom 弹架构, 回复关键字“idea”，即可免费获得激活码

1. 重置脚本，其原理是删除 jetbrains 全系列产品软件试用相关目录（其他配置不受影响）

```sh
#!/bin/sh
# reset jetbrains ide evals

OS_NAME=$(uname -s)
JB_PRODUCTS="IntelliJIdea CLion PhpStorm GoLand PyCharm WebStorm Rider DataGrip RubyMine AppCode"

if [ $OS_NAME == "Darwin" ]; then
	echo 'macOS:'

	for PRD in $JB_PRODUCTS; do
    	rm -rf ~/Library/Preferences/${PRD}*/eval
    	rm -rf ~/Library/Application\ Support/JetBrains/${PRD}*/eval
	done
elif [ $OS_NAME == "Linux" ]; then
	echo 'Linux:'

	for PRD in $JB_PRODUCTS; do
    	rm -rf ~/.${PRD}*/config/eval
    	rm -rf ~/.config/JetB${PRD}*/eval
	done
else
	echo 'unsupport'
	exit
fi

echo 'done.'
```

2. idea 中：Help -> Eidt Custom VM Options... 

添加如下配置：

```text
-javaagent:[idea-dir]/FineAgent.jar
```

3. 重启之后输入激活码

```text
5AYV1D1RE5-eyJsaWNlbnNlSWQiOiI1QVlWMUQxUkU1IiwibGljZW5zZWVOYW1lIjoiaHR0cHM6Ly93d3cuaml3ZWljaGVuZ3podS5jb20iLCJhc3NpZ25lZU5hbWUiOiIiLCJhc3NpZ25lZUVtYWlsIjoiIiwibGljZW5zZVJlc3RyaWN0aW9uIjoiIiwiY2hlY2tDb25jdXJyZW50VXNlIjpmYWxzZSwicHJvZHVjdHMiOlt7ImNvZGUiOiJJSSIsImZhbGxiYWNrRGF0ZSI6IjIwOTktMTItMzEiLCJwYWlkVXBUbyI6IjIwOTktMTItMzEifSx7ImNvZGUiOiJBQyIsImZhbGxiYWNrRGF0ZSI6IjIwOTktMTItMzEiLCJwYWlkVXBUbyI6IjIwOTktMTItMzEifSx7ImNvZGUiOiJEUE4iLCJmYWxsYmFja0RhdGUiOiIyMDk5LTEyLTMxIiwicGFpZFVwVG8iOiIyMDk5LTEyLTMxIn0seyJjb2RlIjoiUFMiLCJmYWxsYmFja0RhdGUiOiIyMDk5LTEyLTMxIiwicGFpZFVwVG8iOiIyMDk5LTEyLTMxIn0seyJjb2RlIjoiR08iLCJmYWxsYmFja0RhdGUiOiIyMDk5LTEyLTMxIiwicGFpZFVwVG8iOiIyMDk5LTEyLTMxIn0seyJjb2RlIjoiRE0iLCJmYWxsYmFja0RhdGUiOiIyMDk5LTEyLTMxIiwicGFpZFVwVG8iOiIyMDk5LTEyLTMxIn0seyJjb2RlIjoiQ0wiLCJmYWxsYmFja0RhdGUiOiIyMDk5LTEyLTMxIiwicGFpZFVwVG8iOiIyMDk5LTEyLTMxIn0seyJjb2RlIjoiUlMwIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IlJDIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IlJEIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IlBDIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IlJNIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IldTIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IkRCIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IkRDIiwiZmFsbGJhY2tEYXRlIjoiMjA5OS0xMi0zMSIsInBhaWRVcFRvIjoiMjA5OS0xMi0zMSJ9LHsiY29kZSI6IlJTVSIsImZhbGxiYWNrRGF0ZSI6IjIwOTktMTItMzEiLCJwYWlkVXBUbyI6IjIwOTktMTItMzEifV0sImhhc2giOiIxMjc5Njg3Ny8wIiwiZ3JhY2VQZXJpb2REYXlzIjo3LCJhdXRvUHJvbG9uZ2F0ZWQiOmZhbHNlLCJpc0F1dG9Qcm9sb25nYXRlZCI6ZmFsc2V9-HNPogO0kWkHCVMnsjmBXUqQt87UPHqdkYqZGveSJtu8hb2V2Yq7gHsHenp4UuEd3jwEwC+YrUIf7U5yDA/56F5Sdn0RLUHZX5DHeQbJPbmYCBsDRT7m8rnmMFOSZn3vwNatvv1cooZbcGOk3Wwxx6bF7XcgaIrmXRcmZMZgv2PZehr0WS1HxNKe3X4nbGP3MwiSbg4ypmxNDrljmgv+Si9QDDwNLDffqeO0Lce0FqEJuMWmvBS42S0aeIYF8IS5bp4+LFKLJ8T7tF40OxKYDurBb9+9c43GZBscM/eLB8Jos66jNGFwgebFUlvhzJKVHZtuc/N8zGeEnTq6K0T/B8w==-MIIDTjCCAjagAwIBAgIBDTANBgkqhkiG9w0BAQsFADAYMRYwFAYDVQQDDA1KZXRQcm9maWxlIENBMCAXDTE4MTEwMTEyMjk0NloYDzIwOTkwODA5MDIyNjA3WjAfMR0wGwYDVQQDDBRwcm9kMnktZnJvbS0yMDIwMTAxOTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAMK3eyr0+Oys/TwcQO+qYaXWGBmXcEP4fR0bHHzZd/4WNGltXhecM80OWthA38BQRYAJBCKz/dSkO2Kj1H2y+7KB5cIaOiJEyTESfTSgzQdot6laRBU8oxy9mmagI46M8zEEmplPybY4YJj4HOwZiBsMQEMxoTgMDLpuHX6uASoVhSj6koB55lOj4wEgsQBeAMWTAXmTl88ixE179J8aBUkBGpL8w/tZzl9BJXZNF15gPfkS58rw8cdPzXLS0Yym37A2/KKFhfHzJc5KhbaxqYzmkAfTMqPsIqCQ1lQUAqfiPn2gN2I1Z3/cQuEW27M55fXVr2DduQe5DWzYJs85L50CAwEAAaOBmTCBljAJBgNVHRMEAjAAMB0GA1UdDgQWBBQk2hEilvWFQcCTR+gxI0z0wIQC/zBIBgNVHSMEQTA/gBSjnrZIZ0ISNkG9beC5tKBSi5fxs6EcpBowGDEWMBQGA1UEAwwNSmV0UHJvZmlsZSBDQYIJANJssYOyg3nhMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAsGA1UdDwQEAwIFoDANBgkqhkiG9w0BAQsFAAOCAQEAsCQBjO5wttco/Z5cj/o4GBrku8UtBBBVFq4xsBanshTHm4deVxcTvta4aScV0TPKcaLqGqWx8A9v8XXO8dBbCuyXvWZteZ/C2Covg1xXiM99lz7VxqpjVmLdKanZn5u0gQSiYJdcfF+TdbmEIeSOnN/kLXNq2hXdJQK2zk2J25UZqu5EibRtTbdOzw6ZcfwJ8uOntXfsmAhnNICP3Wf/4wR/mwB0Ka4S+JA3IbF5MUmUZ/fjUaFarnin70us+Vxf/sZUi7u67wilvwVV0NAqDpthHUV0NRc4q+yOr2Dt/uCHdy4XRXLJfAv/z9/xBwNZZALNz3EtQL6IeIWWJByl3g==
```

