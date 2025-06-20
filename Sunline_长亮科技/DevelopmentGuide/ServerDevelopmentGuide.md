# Server Development Guide

## Tools

### IntelliJ IDEA 2023.3.3 (Community Edition)

**配置**

* Build, Execution, Deployment.Build Tools.Maven.Runner.VM Options

  ![image-20250605160403205](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250605160403205.png)

  ```
  --add-opens java.base/java.lang=ALL-UNNAMED
  ```

## Run

### 虚拟机参数

```
--add-opens java.base/java.lang=ALL-UNNAMED --add-opens java.base/java.lang.reflect=ALL-UNNAMED --add-opens java.base/java.math=ALL-UNNAMED --add-opens java.base/java.util=ALL-UNNAMED --add-opens java.base/java.net=ALL-UNNAMED --add-opens java.base/java.text=ALL-UNNAMED --add-opens java.base/sun.nio.ch=ALL-UNNAMED
```

## APStack

### Base Type Mapping

| Type      | APS Type               |
| --------- | ---------------------- |
| timestamp | BaseType.U_TXN_TM_INTG |
| flag      | EftipltEnum.E_WTHR_FLG |

### Http

1. Get raw http request and raw http response.

   ```java
   HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();
           HttpServletResponse response = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getResponse();
   ```

   
