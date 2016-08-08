title: Realm在iOS实际开发中的应用
date: 2016-01-21 14:42:02
tags: Realm
---

项目具体可以见我开源的Meizi项目，一个抓取网页源代码做成的App，暂时没有做完。

<div class="github-widget" data-repo="zcill/Meizi"></div>

### 基本功能

#### 建一个Realm模型，可以直接当做Model来用
```
// 虽然可以直接拿过来当做Model来用，但是注意Realm支持的类型
#import <Realm/Realm.h>

@interface ZCMainRealm : RLMObject

    @property (nonatomic, assign) NSInteger ID;
    @property (nonatomic, copy) NSString *name;
    @property (nonatomic, assign) NSInteger age;
    @property (nonatomic, copy) NSString *sex;

@end

// This protocol enables typed collections. i.e.:
// RLMArray<ZCMainRealm>
RLM_ARRAY_TYPE(ZCMainRealm)
```
> Realm支持的类型:BOOL、bool、int、NSInteger、long、long long、float、double、NSString、NSDate （精度为秒）、NSData 以及 被特殊类型标记的 NSNumber 。
> 
> 详见Realm的中文文档[Objective-C Docs - Realm is a mobile database: a replacement for SQLite & Core Data](https://realm.io/cn/docs/objc/latest/#section-5)

#### 增删改查，基本的数据库功能

```
// 获取Realm数据库
RLMRealm *realm = [RLMRealm defaultRealm];

    // 打开数据库事务
    [realm transactionWithBlock:^{

        ZCMainRealm *person = [[ZCMainRealm alloc] init];

        person.ID = 123;
        person.name = @"w";
        person.age = 15;
        person.sex = @"female";

        // 添加到数据库
        [realm addObject:person];

        // 提交事务
        [realm commitWriteTransaction];

    }];
```

```
    // 查询数据库
    RLMResults *tempArray = [ZCMainRealm allObjects];

    for (ZCMainRealm *person in tempArray) {

        NSLog(@"%ld %@ %ld %@", person.ID, person.name, person.age, person.sex);

    }
```

```
// 修改数据库
    [realm transactionWithBlock:^{

        // 获取所有对象
        RLMResults *result = [ZCMainRealm allObjects];

        // 获取第一个对象
        ZCMainRealm *firstRealm = [result objectAtIndex:0];

        // 修改sex
        firstRealm.sex = @"male";

        // 提交事务
        [realm commitWriteTransaction];

    }];
```

```
// 删除数据库对象
    [realm transactionWithBlock:^{

        // 获得对象
        RLMResults *rusults = [ZCMainRealm allObjects];

        // 清空
        [realm deleteObject:rusults.firstObject];

    }];

    NSLog(@"%@", realm.path);
```

### 实际开发中常用方式

#### 用作本地缓存
```
// 把妹子的数据放进本地数据库
- (void)addMeiziDataIntoRealm {

    RLMRealm *realm = [RLMRealm defaultRealm];
    RLMResults *results = [ZCMeiziRealm allObjects];

    if (results.count == 0) {
        [self realm_add_inRealm:realm];
    } else {
        [realm beginWriteTransaction];
        [self realm_update_inRealm:realm];
    }

    [self.collectionView reloadData];

    NSLog(@"%@", realm.path);
}

/**
 *  把妹子的thumbImageUrl和title存进数据库
 *
 *  @param realm 数据库
 */
- (void)realm_add_inRealm:(RLMRealm *)realm {

    [realm transactionWithBlock:^{

        for (NSDictionary *dict in _dataSource) {

            ZCMeiziRealm *girlRealm = [[ZCMeiziRealm alloc] init];
            girlRealm.meiziTitle = dict[@"alt"];
            girlRealm.meiziImageUrl = dict[@"data-original"];
            girlRealm.meiziUrl = [[_detailUrlDict objectForKey:girlRealm.meiziTitle] objectForKey:@"href"];

            [realm addObject:girlRealm];
        }

        [realm commitWriteTransaction];

    }];

}

/**
 *  用于刷新列表的更新数据，防止插入相同的数据
 *
 *  @param realm 数据库
 */
- (void)realm_update_inRealm:(RLMRealm *)realm {

    for (NSDictionary *dict in _dataSource) {

        ZCMeiziRealm *girlRealm = [[ZCMeiziRealm alloc] init];
        girlRealm.meiziTitle = dict[@"alt"];
        girlRealm.meiziImageUrl = dict[@"data-original"];
        girlRealm.meiziUrl = [[_detailUrlDict objectForKey:girlRealm.meiziTitle] objectForKey:@"href"];

        [realm addOrUpdateObject:girlRealm];

        // 两种方法更新条目，都可以使用，但是都要求在数据库的表中设置了主键
//        [ZCMeiziRealm createOrUpdateInRealm:realm withValue:girlRealm];

    }
    [realm commitWriteTransaction];


}
```