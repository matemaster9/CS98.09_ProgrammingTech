---
description: 利用IOC容器实现策略
---

# 基于容器实现策略模式

ApplicationContext （IOC容器）+ ActivityPrepareService（抽象策略接口）

```java
@Component
@RequiredArgsConstructor
public class ActivityPrepareHelper {

    private final ApplicationContext applicationContext;
    private Map<ActivityPrepareTypeEnum, ActivityPrepareService> activityPrepareServiceMap;

    @PostConstruct
    public void initActivityPrepareServiceMap() {
        activityPrepareServiceMap = ImmutableMap
                .<ActivityPrepareTypeEnum, ActivityPrepareService>builder()
                .put(ActivityPrepareTypeEnum.LiveStreaming, applicationContext.getBean(LiveStreamingStrategy.class))
                .put(ActivityPrepareTypeEnum.PrizeAward, applicationContext.getBean(PrizeAwardStrategy.class))
                .put(ActivityPrepareTypeEnum.OtherActivity, applicationContext.getBean(OtherActivityStrategy.class))
                .build();
    }

    public List<String> getAuditors(ActivityPrepareDto activityPrepare) {
        ActivityPrepareTypeEnum activityPrepareTypeEnum = ActivityPrepareTypeEnum.getInstanceByCode(activityPrepare.getActivityPrepareType());
        if (activityPrepareTypeEnum == null) {
            throw new IllegalParamsException("活动报备类型不存在");
        }
        ActivityPrepareService activityPrepareService = activityPrepareServiceMap.get(activityPrepareTypeEnum);
        return activityPrepareService.getAuditors(activityPrepare.getPersonalizedContent());
    }

    public List<String> getWellKnownPeople(ActivityPrepareDto activityPrepare) {
        ActivityPrepareTypeEnum activityPrepareTypeEnum = ActivityPrepareTypeEnum.getInstanceByCode(activityPrepare.getActivityPrepareType());
        if (activityPrepareTypeEnum == null) {
            throw new IllegalParamsException("活动报备类型不存在");
        }
        ActivityPrepareService activityPrepareService = activityPrepareServiceMap.get(activityPrepareTypeEnum);
        return activityPrepareService.getWellKnownPeople(activityPrepare.getPersonalizedContent());
    }
}
```

抽象接口策略草图

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>业务服务接口</p></figcaption></figure>

业务背景：

利用活动报备的类型获取配置数据中的责任人，不同类型对于不同责任人

这里的具体策略就是对应具体活动报备类型，每个策略都针对不同类型数据进行处理逻辑，可以有效避免大量的if-else代码冗余
