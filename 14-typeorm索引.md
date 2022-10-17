`@Index()` 装饰器在列上定义一个 “索引” 加速查询 也可以应用在实体类上，传入字段名，定义多个“索引”

```js
import { Column, Entity, PrimaryGeneratedColumn, Index } from 'typeorm';

@Index(['name', 'type'])
@Entity()
export class Event {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  type: string;

  @Index()
  @Column()
  name: string;

  @Column('json')
  payload: Record<string, any>;
}

```

