抽象工厂模式**提供了一个创建一系列相关或相互依赖对象的接口**，而无需指定它们具体的类。
![在这里插入图片描述](https://gitee.com/zisuu/picture/raw/master/img/20210103100549.png)
抽象工厂模式的主要角色如下：

抽象工厂（Abstract Factory）：提供了创建产品的接口，它包含多个创建产品的方法 newProduct()，可以创建多个不同等级的产品。
具体工厂（Concrete Factory）：主要是实现抽象工厂中的多个抽象方法，完成具体产品的创建。
抽象产品（Product）：定义了产品的规范，描述了产品的主要特性和功能，抽象工厂模式有多个抽象产品。
具体产品（ConcreteProduct）：实现了抽象产品角色所定义的接口，由具体工厂来创建，它 同具体工厂之间是多对一的关系。

### 实例

Nike公司和lining公司都会生产运动品牌产品，包括服装产品(夹克，T恤)和鞋类产品(跑鞋，篮球鞋)。

下面用代码来实现。

服装类产品

```java
/**
 * 服装产品
 */
public interface Clothes {

    void printUsage();

}

/**
 * 夹克
 */
public class JackClothes implements Clothes {
    
    @Override
    public void printUsage() {
        System.out.println("我是夹克衫，穿我很潮");
    }
    
}

/**
 * T恤
 */
public class TShirtClothes implements Clothes {

    @Override
    public void printUsage() {
        System.out.println("我是T恤，随意");
    }

}
```

鞋类产品

```java
/**
 * 鞋类产品
 */
public interface Shoe {
    
    void describeSelf();
}

/**
 * 跑鞋(具体的鞋类产品)
 */
public class RunningShoe implements Shoe {

    @Override
    public void describeSelf() {
        System.out.println("我是跑鞋，我为自己带盐");
    }

}

/**
 * 篮球鞋(具体的鞋类产品)
 */
public class BasketballShoe implements Shoe {
    
    @Override
    public void describeSelf() {
        System.out.println("我是篮球鞋，穿我打篮球吊到爆");    
    }
    
}
```

工厂

```java
/**
 * 抽象工厂
 */
public interface Factory {

    /**
     * 既生产鞋类产品
     */
    Shoe produceShoe(BRAND brand);

    /**
     * 又能生产服装类商品
     */
    Clothes produceClothes(BRAND brand);
}

/**
 * 李宁工厂，生产鞋系列产品，也生产服装系列产品
 */
public class LiningFactory implements Factory {

    /**
     * 生产鞋系列产品
     */
    @Override
    public Shoe produceShoe(BRAND brand) {
        if (BRAND.BASKETBALL_SHOE == brand) {
            System.out.println("---李宁工厂生产篮球鞋---");
            return new BasketballShoe();
        }

        if (BRAND.RUNNING_SHOE == brand) {
            System.out.println("---李宁工厂生产跑鞋---");
            return new RunningShoe();
        }

        return null;
    }

    /**
     * 生产服装系列产品
     */
    @Override
    public Clothes produceClothes(BRAND brand) {
        if (BRAND.JACK_CLOTHES == brand) {
            System.out.println("---李宁工厂生产夹克---");
            return new JackClothes();
        }

        if (BRAND.TSHIRT_CLOTHES == brand) {
            System.out.println("---李宁工厂生产T恤---");
            return new TShirtClothes();
        }

        return null;
    }
}

/**
 * Nike工厂，生产鞋系列产品，也生产服装系列产品
 */
public class NikeFactory implements Factory {

    /**
     * 生产鞋子系列产品
     */
    @Override
    public Shoe produceShoe(BRAND brand) {
        if (BRAND.RUNNING_SHOE == brand) {
            System.out.println("---Nike工厂生产篮球鞋---");
            return new RunningShoe();
        }

        if (BRAND.BASKETBALL_SHOE == brand) {
            System.out.println("---Nike工厂生产跑鞋---");
            return new BasketballShoe();
        }

        return null;
    }

    /**
     * 生产服装系列产品
     */
    @Override
    public Clothes produceClothes(BRAND brand) {
        if (BRAND.JACK_CLOTHES == brand) {
            System.out.println("---Nike工厂生产夹克---");
            return new JackClothes();
        }

        if (BRAND.TSHIRT_CLOTHES == brand) {
            System.out.println("---Nike工厂生产T恤---");
            return new TShirtClothes();
        }

        return null;
    }

}
```

客户端代码

```java
public class Client {

    public static void main(String[] args) {
        // Nike工厂，生产鞋系列产品(篮球鞋，跑鞋)和服装系列产品(夹克，T恤)
        Factory nikeFactory = new NikeFactory();
        // 生产鞋系列产品
        Shoe nikeBasketballShoe = nikeFactory.produceShoe(BRAND.BASKETBALL_SHOE);// 篮球鞋
        Shoe nikeRunningshoe = nikeFactory.produceShoe(BRAND.RUNNING_SHOE);// 跑鞋
        nikeBasketballShoe.describeSelf();
        nikeRunningshoe.describeSelf();
        // 生产服装系列产品
        Clothes nikeJack = nikeFactory.produceClothes(BRAND.JACK_CLOTHES);// 夹克衫
        Clothes nikeTshit = nikeFactory.produceClothes(BRAND.TSHIRT_CLOTHES);// T恤
        nikeJack.printUsage();
        nikeTshit.printUsage();

        System.out.println("============================");

        // 李宁工厂，生产鞋系列产品(篮球鞋，跑鞋)和服装系列产品(夹克，T恤)
        Factory liningFactory = new LiningFactory();
        // 生产鞋系列产品
        Shoe liningBasketballShoe = liningFactory.produceShoe(BRAND.BASKETBALL_SHOE);
        Shoe liningRunningShoe = liningFactory.produceShoe(BRAND.RUNNING_SHOE);
        liningBasketballShoe.describeSelf();
        liningRunningShoe.describeSelf();
        // 生产服装系列产品
        Clothes liningJack = liningFactory.produceClothes(BRAND.JACK_CLOTHES);
        Clothes liningTshirt = liningFactory.produceClothes(BRAND.TSHIRT_CLOTHES);
        liningJack.printUsage();
        liningTshirt.printUsage();
    }
}
```