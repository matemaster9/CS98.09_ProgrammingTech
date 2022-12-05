---
description: 基于 Lombok注解：@Getter + @Builder自动生成建造者模式代码
---

# 建造者模式

```java
public class CompanyMock {
    private int rank;
    private String companyName;
    private long income;
    private long profit;
    private String country;
    private String founder;

    // 基于java-faker建造mock数据：创建具备实体意义的对象
    public static CompanyMockBuilder mockBuilder() {
        Faker faker = new Faker();
        return builder()
                .rank(faker.number().randomDigitNotZero())
                .companyName(faker.company().name())
                .income(faker.number().randomNumber())
                .profit(faker.number().randomNumber())
                .founder(faker.name().fullName())
                .country(faker.country().name());
    }
    
    CompanyMock(int rank, String companyName, long income, long profit, String country, String founder) {
        this.rank = rank;
        this.companyName = companyName;
        this.income = income;
        this.profit = profit;
        this.country = country;
        this.founder = founder;
    }

    public static CompanyMockBuilder builder() {
        return new CompanyMockBuilder();
    }
    
    // 内部建造者类 + 方法链
    public static class CompanyMockBuilder {
        private int rank;
        private String companyName;
        private long income;
        private long profit;
        private String country;
        private String founder;

        CompanyMockBuilder() {
        }

        public CompanyMockBuilder rank(int rank) {
            this.rank = rank;
            return this;
        }

        public CompanyMockBuilder companyName(String companyName) {
            this.companyName = companyName;
            return this;
        }

        public CompanyMockBuilder income(long income) {
            this.income = income;
            return this;
        }

        public CompanyMockBuilder profit(long profit) {
            this.profit = profit;
            return this;
        }

        public CompanyMockBuilder country(String country) {
            this.country = country;
            return this;
        }

        public CompanyMockBuilder founder(String founder) {
            this.founder = founder;
            return this;
        }

        public CompanyMock build() {
            return new CompanyMock(this.rank, this.companyName, this.income, this.profit, this.country, this.founder);
        }
    }
}
```
