swagger: '2.0'

```
# API 文档的相关信息  
info:  
  version: "0.0.0"  
  title: "test swagger"  
```  
```  
# API 路径  
paths: 
  /path1:
    # API 方法  
    get:
      # 描述方法(Markdown Able)  
      description: |
        >>>>>>>>>>>>>
        >>>>>>>>>>>>>
      # API 方法需要的参数  
      parameters:
        # 参数 1 
        - 
          name: size # 参数名称
          in: query  # 参数位置
          desdcription: # 对参数的描述
            >>>>>>>>>>>>
          required: true # 参数是否强制需要  
          type: number   # 参数类型
          format: double # 参数格式 ？
      # API 方法拿到的响应  
      responses:
        # 相应 Status  
        200: 
          # 对响应结果的描述  
          description: Successful response  
          # 响应返回结果的数据结构
          schema: 
            # 返回值名称
            title: ArrayOfPersons  
            # 返回值类型
            type: array
            # 因为返回值是数组，因此数组中每个对象也需要描述  
            items:
              title: Person
              type: object
              # Object 对象中有多个子的字段
              properties:
                name: 
                  type: string  
                single:
                  type: boolean
```  



