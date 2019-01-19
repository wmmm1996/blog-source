---
title: 状态模式-State Pattern
date: 2019-01-19 14:11:18
tags:
  - java
  - 设计模式
categories: 
  - JAVA设计模式
---

# 概述
> 允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它的类。

<!-- more -->

# 组成

- Context（环境类）
> 环境类又称为上下文类，它是拥有多种状态的对象。由于环境类的状态存在多样性且在不同状态下对象的行为有所不同，因此将状态独立出去形成单独的状态类。在环境类中维护一个抽象状态类State的实例，这个实例定义当前状态，在具体实现时，它是一个State子类的对象。
- State（抽象状态类）
> 它用于定义一个接口以封装与环境类的一个特定状态相关的行为，在抽象状态类中声明了各种不同状态对应的方法，而在其子类中实现类这些方法，由于不同状态下对象的行为可能不同，因此在不同子类中方法的实现可能存在不同，相同的方法可以写在抽象状态类中。
- ConcreteState（具体状态类）
> 它是抽象状态类的子类，每一个子类实现一个与环境类的一个状态相关的行为，每一个具体状态类对应环境的一个具体状态，不同的具体状态类其行为有所不同。

# 适用场景

- 对象的行为依赖于它的状态（如某些属性值），状态的改变将导致行为的变化。
- 在代码中包含大量与对象状态有关的条件语句，这些条件语句的出现，会导致代码的可维护性和灵活性变差，不能方便地增加和删除状态，并且导致客户类与类库之间的耦合增强。

# 案例

公司欲为某银行开发一套信用卡业务系统，银行账户(Account)是该系统的核心类之一，通过分析，公司开发人员发现在该系统中，账户存在三种状态，且在不同状态下账户存在不同的行为，具体说明如下：
  
1. 如果账户中余额大于等于0，则账户的状态为正常状态(Normal State)，此时用户既可以向该账户存款也可以从该账户取款；
2. 如果账户中余额小于0，并且大于-2000，则账户的状态为透支状态(Overdraft State)，此时用户既可以向该账户存款也可以从该账户取款，但需要按天计算利息；
3. 如果账户中余额等于-2000，那么账户的状态为受限状态(Restricted State)，此时用户只能向该账户存款，不能再从中取款，同时也将按天计算利息；
4. 根据余额的不同，以上三种状态可发生相互转换。

![](https://i.imgur.com/XeBQbxh.png)
> 银行账户状态图

## 反例设计

设计了一个较为庞大的账户类Account，其中部分代码如下所示：

```java
class Account {
	private String state; //状态
	private int balance; //余额
	
	//存款操作	
	public void deposit() {
		//存款
		stateCheck();	
	}
	
	//取款操作
	public void withdraw() {
		if (state.equalsIgnoreCase("NormalState") || state.equalsIgnoreCase("OverdraftState ")) {
			//取款
			stateCheck();
		}
		else {
			//取款受限
		}
	}
	
	//计算利息操作
	public void computeInterest() {
		if(state.equalsIgnoreCase("OverdraftState") || state.equalsIgnoreCase("RestrictedState ")) {
			//计算利息
		}
	}
	
	//状态检查和转换操作
	public void stateCheck() {
		if (balance >= 0) {
			state = "NormalState";
		}
		else if (balance > -2000 && balance < 0) {
			state = "OverdraftState";
		}
		else if (balance == -2000) {
			state = "RestrictedState";
		}
        else if (balance < -2000) {
            //操作受限
        }
	}
}
```

上述代码的问题:

- 代码非常冗长，可维护性较差
> 几乎每个方法中都包含状态判断语句，以判断在该状态下是否具有该方法以及在特定状态下该方法如何实现，导致代码非常冗长，可维护性较差
- 较为复杂的stateCheck()方法
> 拥有一个较为复杂的stateCheck()方法，包含大量的if…else if…else…语句用于进行状态转换，代码测试难度较大，且不易于维护
- 系统扩展性较差
> 如果需要增加一种新的状态，如冻结状态（Frozen State，在该状态下既不允许存款也不允许取款），需要对原有代码进行大量修改，扩展起来非常麻烦

## 解决方案
> 状态模式可以在一定程度上解决上述问题

```java
//银行账户：环境类
public class Account {
    @Setter
    private AccountState state; //维持一个对抽象状态对象的引用
    private String owner; //开户名
    @Getter
    @Setter
    private double balance = 0; //账户余额

    public Account(String owner, double init) {
        this.owner = owner;
        this.balance = balance;
        this.state = new NormalState(this); //设置初始状态
        System.out.println(this.owner + "开户，初始金额为" + init);
        System.out.println("---------------------------------------------");
    }

    public void deposit(double amount) {
        System.out.println(this.owner + "存款" + amount);
        state.deposit(amount); //调用状态对象的deposit()方法
        System.out.println("现在余额为" + this.balance);
        System.out.println("现在帐户状态为" + this.state.getClass().getName());
        System.out.println("---------------------------------------------");
    }

    public void withdraw(double amount) {
        System.out.println(this.owner + "取款" + amount);
        state.withdraw(amount); //调用状态对象的withdraw()方法
        System.out.println("现在余额为" + this.balance);
        System.out.println("现在帐户状态为" + this.state.getClass().getName());
        System.out.println("---------------------------------------------");
    }

    public void computeInterest() {
        state.computeInterest(); //调用状态对象的computeInterest()方法
    }
}
//抽象状态类
public abstract class AccountState {
    protected Account acc;
    public abstract void deposit(double amount);
    public abstract void withdraw(double amount);
    public abstract void computeInterest();
    public abstract void stateCheck();
}
//正常状态：具体状态类
public class NormalState extends AccountState {
    public NormalState(Account acc) {
        this.acc = acc;
    }

    public NormalState(AccountState state) {
        this.acc = state.acc;
    }

    @Override
    public void deposit(double amount) {
        acc.setBalance(acc.getBalance() + amount);
        stateCheck();
    }

    @Override
    public void withdraw(double amount) {
        acc.setBalance(acc.getBalance() - amount);
        stateCheck();
    }

    @Override
    public void computeInterest() {
        System.out.println("正常状态，无须支付利息！");
    }

    //状态转换
    @Override
    public void stateCheck() {
        if (acc.getBalance() > -2000 && acc.getBalance() <= 0) {
            acc.setState(new OverdraftState(this));
        } else if (acc.getBalance() == -2000) {
            acc.setState(new RestrictedState(this));
        } else if (acc.getBalance() < -2000) {
            System.out.println("操作受限！");
        }
    }
}
//透支状态：具体状态类
public class OverdraftState extends AccountState {
    public OverdraftState(AccountState state) {
        this.acc = state.acc;
    }

    @Override
    public void deposit(double amount) {
        acc.setBalance(acc.getBalance() + amount);
        stateCheck();
    }

    @Override
    public void withdraw(double amount) {
        acc.setBalance(acc.getBalance() - amount);
        stateCheck();
    }

    @Override
    public void computeInterest() {
        System.out.println("计算利息！");
    }

    //状态转换
    @Override
    public void stateCheck() {
        if (acc.getBalance() > 0) {
            acc.setState(new NormalState(this));
        }
        else if (acc.getBalance() == -2000) {
            acc.setState(new RestrictedState(this));
        }
        else if (acc.getBalance() < -2000) {
            System.out.println("操作受限！");
        }
    }
}
//受限状态：具体状态类
public class RestrictedState extends AccountState {
    public RestrictedState(AccountState state) {
        this.acc = state.acc;
    }

    @Override
    public void deposit(double amount) {
        acc.setBalance(acc.getBalance() + amount);
        stateCheck();
    }

    @Override
    public void withdraw(double amount) {
        System.out.println("帐号受限，取款失败");
    }

    @Override
    public void computeInterest() {
        System.out.println("计算利息！");
    }

    //状态转换
    @Override
    public void stateCheck() {
        if(acc.getBalance() > 0) {
            acc.setState(new NormalState(this));
        }
        else if(acc.getBalance() > -2000) {
            acc.setState(new OverdraftState(this));
        }
    }
}
```

客户端:

```java
public class Client {
    public static void main(String[] args) {
        Account acc = new Account("段誉", 0.0);
        acc.deposit(1000);
        acc.withdraw(2000);
        acc.deposit(3000);
        acc.withdraw(4000);
        acc.withdraw(1000);
        acc.computeInterest();
    }
}
```

输出结果

```
段誉开户，初始金额为0.0
---------------------------------------------
段誉存款1000.0
现在余额为1000.0
现在帐户状态为statePattern.NormalState
---------------------------------------------
段誉取款2000.0
现在余额为-1000.0
现在帐户状态为statePattern.OverdraftState
---------------------------------------------
段誉存款3000.0
现在余额为2000.0
现在帐户状态为statePattern.NormalState
---------------------------------------------
段誉取款4000.0
现在余额为-2000.0
现在帐户状态为statePattern.RestrictedState
---------------------------------------------
段誉取款1000.0
帐号受限，取款失败
现在余额为-2000.0
现在帐户状态为statePattern.RestrictedState
---------------------------------------------
计算利息！
```

# 优缺点

## 优点
- 封装了状态的转换规则
> 在状态模式中可以将状态的转换代码封装在环境类或者具体状态类中，可以对状态转换代码进行集中管理，而不是分散在一个个业务方法中
- 将所有与某个状态有关的行为放到一个类中
> 只需要注入一个不同的状态对象即可使环境对象拥有不同的行为
- 允许状态转换逻辑与状态对象合成一体，而不是提供一个巨大的条件语句块
> 状态模式可以让我们避免使用庞大的条件语句来将业务方法和状态转换代码交织在一起
- 让多个环境对象共享一个状态对象
> 从而减少系统中对象的个数

## 缺点
- 必然会增加系统中类和对象的个数，导致系统运行开销增大
- 如果使用不当将导致程序结构和代码的混乱，增加系统设计的难度
- 对“开闭原则”的支持并不太好
> 增加新的状态类需要修改那些负责状态转换的源代码，否则无法转换到新增状态；而且修改某个状态类的行为也需修改对应类的源代码。

---
👉 [本文代码](https://github.com/gcdd1993/java-design-pattern/tree/master/src/main/java/statePattern)
👉 [返回设计模式概览](#JAVA设计模式/设计模式概览)