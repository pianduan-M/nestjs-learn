Service 用来写业务代码

1. 命令生成 service 文件 会自动将该 service 添加到 module > providers

   ```shell
   nest g s
   
   ? What name would you like to use for the service? coffees
   CREATE src/coffees/coffees.service.spec.ts (467 bytes)
   CREATE src/coffees/coffees.service.ts (91 bytes)
   UPDATE src/app.module.ts (410 bytes)
   ```

   coffees.service.ts

   ```
   import { Injectable } from '@nestjs/common';
   
   @Injectable()
   export class CoffeesService {}
   
   ```

   app.module.ts

   ```js
   @Module({
     imports: [],
     controllers: [AppController, CoffeesController],
     providers: [AppService, CoffeesService],
   })
   ```

2. 注入到 contriller 中 只需到 controller 类 constructor 声明参数即可

   ```js
   export class CoffeesController {
     constructor(private readonly coffeesService: CoffeesService) {}
   }
   ```

   

