1. ValidationPipe 用来验证请求参数 全局使用后就可以使用 class-validator 库来做参数验证，

   Main.ts

   ```
   async function bootstrap() {
     const app = await NestFactory.create(AppModule);
     app.useGlobalPipes(new ValidationPipe());
     await app.listen(3000);
   }
   ```

   白名单 `whitelist: true` 自动剥离我们不需要的或者说没有在 dto 声明的字段

   ```js
   import { ValidationPipe } from '@nestjs/common';
   import { NestFactory } from '@nestjs/core';
   import { AppModule } from './app.module';
   
   async function bootstrap() {
     const app = await NestFactory.create(AppModule);
     app.useGlobalPipes(
       new ValidationPipe({
         whitelist: true,
       }),
     );
     await app.listen(3000);
   }
   bootstrap();
   
   
   // request body
   	{
       "name": "Shipwreck Roast",
       "brand": "Buddy Brew",
       "flavors": [
           "caramel"
       ],
       "isEnabled": true
   	}
   // response 
   	{
       "name": "Shipwreck Roast",
       "brand": "Buddy Brew",
       "flavors": [
           "caramel"
       ]
   	}
   ```

   禁止非白名单 `forbidNonWhitelisted` 配合 `whitelist` 开启后出现 dto 规定之外的字段将会报错

   ```js
    app.useGlobalPipes(
       new ValidationPipe({
         whitelist: true,
         forbidNonWhitelisted: true,
       }),
     );
   
   // request body
   	{
       "name": "Shipwreck Roast",
       "brand": "Buddy Brew",
       "flavors": [
           "caramel"
       ],
       "isEnabled": true
   	}
   // response 
   	{
       "statusCode": 400,
       "message": [
           "property isEnabled should not exist"
       ],
       "error": "Bad Request"
   	}
   ```

   `transform` 自动转换原始类型

   main.ts

   ```js
     app.useGlobalPipes(
       new ValidationPipe({
         whitelist: true,
         transform: true,
         forbidNonWhitelisted: true,
       }),
     );
   ```

   coffees.controller.ts

   ```js
     @Get(':id')
     findOne(@Param('id') id: number) {
       console.log(typeof id); // number	
     }
   ```

   

2. class-validator 用来验证数据类型 如果参数少了或者规定的数据类型不对，则会返回错误 400 

   ```js
   npm i class-validator
   
   // create-coffee.dto.ts
   import { IsString } from 'class-validator';
   
   export class CreateCoffeeDto {
     @IsString()
     readonly name: string;
   
     @IsString()
     readonly brand: string;
   
     @IsString({ each: true })
     readonly flavors: string[];
   }
   
   // request body
   	{
       "name": "Old Florida Roast"
   	}
   // response 
   	{
       "statusCode": 400,
       "message": [
           "brand must be a string",
           "each value in flavors must be a string"
       ],
       "error": "Bad Request"
   	}
   
   // request body
   	{
       "name": "Shipwreck Roast",
       "brand": "Buddy Brew",
       "flavors": [
           1,
           2
       ]
   	}
   // response 
   	{
       "statusCode": 400,
       "message": [
           "each value in flavors must be a string"
       ],
       "error": "Bad Request"
   	}
   
   ```

   



3. class-transformer 用来转换数据类型

4. @nestjs/mapped-types 中 PartialType 方法返回传入类型，并且所有字段都变成可选的，并继承传入类型的验证方法  减少重复代码

   ```shell
   npm i @nestjs/mapped-types -S
   ```

   update-coffee.dto.ts

   ```js
   
   import { PartialType } from '@nestjs/mapped-types';
   import { CreateCoffeeDto } from './create-coffee.dto';
   
   export class UpdateCoffeeDto extends PartialType(CreateCoffeeDto) {}
   
   ```

   