1. 创建

   使用 `nest g co` 输入名称将创建同名文件夹 输入 `--no-sepc` 不会创建测试文件

   ```js
   nest g co
   
   ? What name would you like to use for the controller? coffees
   
   CREATE src/coffees/coffees.controller.spec.ts (499 bytes)
   CREATE src/coffees/coffees.controller.ts (103 bytes)
   UPDATE src/app.module.ts (334 bytes)
   ```

2. `@Controller` controller 装饰器

   `@Controller` 装饰器传入字符串将作为该模块请求前缀路径

   ```js
   import { Controller } from '@nestjs/common';
   
   @Controller('coffees')
   export class CoffeesController {}
   
   ```

3. `@Get` 装饰器

   `@Get` 装饰器修饰方法为 get 请求，传入字符串为当前接口嵌套 url 可以不传

   ```js
   export class CoffeesController {
     @Get()
     findAll() {
       return `this action returns all coffees`;
     }
   }
   
   // http://localhost:3000/coffees
   // this action returns all coffees
   
   // 嵌套 url
   
   export class CoffeesController {
     @Get()
     findAll('flavors') {
       return `this action returns all coffees`;
     }
   }
   
   // http://localhost:3000/coffees/flavors
   // this action returns all coffees
   
   ```

    

4. 动态参数 `:id` 

   装饰器内传入 `:id` 表示 url 中有一个名为 id 的动态参数，使用 `@Param` 装饰器接收 装饰器如果不传入参数则表示接受所有请求出纳室

   ```js
   export class CoffeesController {
     @Get(':id')
     findOne(@Param() params) {
       return `This action returns #${params.id} coffee`;
     }
   }
   
   // http://localhost:3000/coffees/123
   // This action returns #123 coffee
   ```

   

5. `@Post` 装饰器

   把下方方法修饰成 post 请求

   ```js
    @Post()
     create(@Body() body) {
       return body;
     }
   
   ```

6. `@Body` 装饰器

   用来接收请求体参数

   ```js
    @Post()
     create(@Body() body) {
       return body;
     }
   	
   	/* 
   		{
       	"name": "Old Florida Roast",
       	"brand": "Buddy Brew"
   		}
   	*/
   	
   		/* 
   		{
       	"name": "Old Florida Roast",
       	"brand": "Buddy Brew"
   		}
   	*/
   	Ï
   
   ```

   传入参数，body将只会得到传入的 key 

   ```
     @Post()
     create(@Body('name') body) {
       return body;
     }
     
   	/* 
   		{
       	"name": "Old Florida Roast",
       	"brand": "Buddy Brew"
   		}
   	*/
   	
   	// Old Florida Roast
   	Ï
   ```

   