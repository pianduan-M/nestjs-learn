1. `@nestjs/typeorm`
2. `typeorm`

3. `pg`

   ```shell
    pnpm i @nestjs/typeorm typeorm pg -S
   ```

   app.module.ts

   ```js
   @Module({
     imports: [
       CoffeesModule,
       TypeOrmModule.forRoot({
         type: 'postgres',
         host: 'localhost',
         port: 5432,
         username: 'postgres',
         password: 'pass123',
         database: 'postgres',
         autoLoadEntities: true,
         synchronize: true, // 同步 使用 @Entiry() 装饰器自动从所有类生成一个 SQL 表，以及他们包含的元数据，适用于开发环境，生产环境勿用
       }),
     ],
     controllers: [AppController, CoffeesController],
     providers: [AppService, CoffeesService],
   })
   ```

   coffee.entity.ts

   ```js
   import { Column, Entity, PrimaryGeneratedColumn } from 'typeorm';
   @Entity()
   export class Coffee {
     @PrimaryGeneratedColumn()
     id: number;
   
     @Column()
     name: string;
   
     @Column()
     brand: string;
   
     @Column('json', { nullable: true  })
     flavors: string[];
   }
   
   ```

   注册实体  

   注册实体时，所有其他模块都将使用 forFeature()  在 forFeature 中传入一个实体数组

   coffees.module.ts   

   ```js
   import { Module } from '@nestjs/common';
   import { TypeOrmModule } from '@nestjs/typeorm';
   import { CoffeesController } from './coffees.controller';
   import { CoffeesService } from './coffees.service';
   import { Coffee } from './entities/coffee.entity';
   
   @Module({
     imports: [TypeOrmModule.forFeature([Coffee])],
     controllers: [CoffeesController],
     providers: [CoffeesService],
   })
   export class CoffeesModule {}
   
   ```

   @ InjectRespository() 注入到 service 中
   
   ```js
   import {
     HttpException,
     HttpStatus,
     Injectable,
     NotFoundException,
   } from '@nestjs/common';
   import { InjectRepository } from '@nestjs/typeorm';
   import { Repository } from 'typeorm';
   import { CreateCoffeeDto } from './dto/create-coffee.dto';
   import { UpdateCoffeeDto } from './dto/update-coffee.dto';
   import { Coffee } from './entities/coffee.entity';
   
   @Injectable()
   export class CoffeesService {
     constructor(
       @InjectRepository(Coffee)
       private readonly coffeeRepository: Repository<Coffee>,
     ) {}
   
     findAll() {
       return this.coffeeRepository.find();
     }
   
     async findOne(id: number) {
       const coffee = await this.coffeeRepository.findOneBy({ id });
       if (!coffee) {
         // throw new HttpException(`Coffee #${id} not found`, HttpStatus.NOT_FOUND);
         throw new NotFoundException(`Coffee #${id} not found`);
       }
       return coffee;
     }
   
     create(createCoffeeDto: CreateCoffeeDto) {
       const coffee = this.coffeeRepository.create(createCoffeeDto);
       return this.coffeeRepository.save(coffee);
     }
   
     async update(id: string, updateCoffeeDto: UpdateCoffeeDto) {
       const coffee = await this.coffeeRepository.preload({
         id: +id,
         ...updateCoffeeDto,
       });
       if (!coffee) {
         throw new NotFoundException(`Coffee #${id} not found`);
       }
       return this.coffeeRepository.save(coffee);
     }
   
     async remove(id: number) {
       const coffee = await this.findOne(id);
       return this.coffeeRepository.remove(coffee);
     }
   }
   
   ```
   
   