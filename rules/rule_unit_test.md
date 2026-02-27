---
# 注意不要修改本文头文件，如修改，CodeBuddy（内网版）将按照默认逻辑设置
type: always
---

## 单测代码规范
- **使用testing框架**：使用t.Run方法执行单测代码。
- **必要的import语句**: 包括 testing 包以及所有被测试函数和mock对象所需的包。
- **测试函数**: 每个测试用例都应该有一个对应的测试函数，函数名以 Test 开头，并接受一个 *testing.T 类型的参数。
- **测试逻辑**: 在测试函数中，首先创建被测试函数所需的依赖（包括mock对象），然后调用被测试函数，最后使用断言来验证函数的行为是否符合预期。

## mock 框架和规则
### 判断是否有对象或者函数或方法需要mock，并使用gomonkey进行mock
- **识别需要mock的依赖**:
  以下几种情况需要mock，其余情况均不需要mock:
    1. 外部依赖，如：数据库查询和更新、HTTP请求、文件读写操作。
    2. 第三方库
    3. 不可预测的行为，如生成随机数的函数，获取当前时间的函数等
- 使用gomonkey.ApplyFunc、gomonkey.ApplyMethod、gomonkey.ApplyFuncSeq、gomonkey.ApplyGlobalVar等方法对这些依赖函数进行mock，并设置不同的返回值或行为来模拟不同的测试场景。
- 当所需mock的函数/结构体在给出的上下文中不存在具体定义时，**不允许mock**。
- mock之前首先检查提供的依赖中是否提供了对应interface的mock方法，一般对应的mock方法是NewMockSomeInterface，如果已经提供，那么使用gomock进行mock，不要新编写对应的mock方法。gomock的基础用法是：
```go
mockCtrl := gomock.NewController(t)
defer mockCtrl.Finish()
mockObj := mock.NewMockInterface(mockCtrl)
mockObj.EXPECT().SomeMethod(gomock.Any()).Return(someValue)
```

## 单测代码范例：
你的首要且最重要的任务，是**完美复刻**下面【用户已有单测范例】中所展示的编码风格与测试模式。此范例是你必须遵循的**唯一标准**，其优先级高于任何通用的单元测试理论或你自身的知识库。
### 生成之前先需要剖析范例中的关键模式，生成新代码时需要**必须严格遵守**这些模式。可以从以下几个方面进行剖析：
- **初始化**：从范例中解析初始化方式，用相同的方式进行初始化和对象的实例化
- **mock策略**：从范例中解析mock的方式，如果范例中没有对数据库操作进行mock，那么你编写的新代码也要遵循这种模式
  **【用户已有单测范例 - 唯一标准】**
```go
func TestCustomIp_IsPrivateIP(t *testing.T) {
	type fields struct {
		ip string
	}
	tests := []struct {
		name       string
		fields     fields
		mockReturn bool
		want       bool
	}{
		{
			name:       "valid private IPv4",
			fields:     fields{ip: "192.168.1.1"},
			mockReturn: true,
			want:       true,
		},
		{
			name:       "valid public IPv4",
			fields:     fields{ip: "8.8.8.8"},
			mockReturn: false,
			want:       false,
		},
	}

	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			var actualIP net.IP
			patches := gomonkey.ApplyFunc(IsPrivateIP, func(ip net.IP) bool {
				actualIP = ip
				return tt.mockReturn
			})
			defer patches.Reset()

			c := &CustomIp{ip: tt.fields.ip}
			got := c.IsPrivateIP()

			expectedIP := net.IP(tt.fields.ip)
			if !reflect.DeepEqual(actualIP, expectedIP) {
				t.Errorf("IsPrivateIP received IP %v, want %v", actualIP, expectedIP)
			}

			if got != tt.want {
				t.Errorf("CustomIp.IsPrivateIP() = %v, want %v", got, tt.want)
			}
		})
	}
}
```
