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
        activityPrepareServiceMap = ImmutableMap.<ActivityPrepareTypeEnum, ActivityPrepareService>builder()
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

