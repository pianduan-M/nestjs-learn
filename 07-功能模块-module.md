将一个功能模块 单独使用一个 module 而不是全部引入到大模块当中

1. 创建 nest g mo

   ```shell
   nest g mo
   ? What name would you like to use for the module? coffees
   CREATE src/coffees/coffees.module.ts (84 bytes)
   UPDATE src/app.module.ts (481 bytes)
   ```

   coffees.module.ts

   ```js
   import { Module } from '@nestjs/common';
   import { CoffeesController } from './coffees.controller';
   import { CoffeesService } from './coffees.service';
   
   @Module({
     imports: [],
     controllers: [CoffeesController],
     providers: [CoffeesService],
   })
   export class CoffeesModule {}
   
   ```

   app.module.ts

   ```js
   @Module({
     imports: [CoffeesModule],
     controllers: [AppController],
     providers: [AppService],
   })
   export class AppModule {}
   ```

   