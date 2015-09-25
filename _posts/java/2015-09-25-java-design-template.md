---
layout: post
title: 设计模式(模板方法)
category: java
keywords: java,模板方法,设计模式
---

之所以叫模板方法,也就是在基类中定义一个流程,基类中实现部分或者是不实现,然后由子类去实现。
直接看代码

定义模板：

```java
public abstract class Game {
	
	protected int playersCount;
	
	/**
	 * 初始游戏,这里定义成private,表示所有初始化都是一样的
	 * 子类不可以自定义初始化内容
	 */
	private void initializeGame(){
		System.out.println("Game init...");
	}
	
	/**
	 * 玩游戏的方法,每个人玩法都不一样
	 * 由子类去实现
	 * @param player
	 */
	abstract void makePlay(int player);
	
	/**
	 * 游戏结束,由子类去实现游戏什么时候结束
	 * @return
	 */
	abstract boolean endOfGame();
	
	/**
	 * 输出胜利的玩家,这里默认实现
	 * 也可以由子类去实现
	 */
	protected void printWinner(){
		System.out.println("Game Winner is...");
	}
	
	/**
	 * 通过子类调用该方法,就可以确定一系列流程
	 * 称之为模板
	 * 也就是同一类流程的入口,流程都是一样的,只是某些实现不一样
	 * @param playersCount
	 */
	public final void playOneGame(int playersCount){
		this.playersCount = playersCount;
		initializeGame();
		
		makePlay(playersCount);
		
		if(endOfGame()){
			printWinner();
		}	
	}
}
```

子类实现：

```java
public class Monopoly extends Game {
	
	@Override
	void makePlay(int player) {
		System.out.println(player + "player is makePlay!");
	}

	@Override
	boolean endOfGame() {
		return true;
	}
	
	protected void printWinner(){
		System.out.println("Game Winner is me!!!");
	}
}
```

```java
public class Player {
	public static void main(String[] args) {
		Monopoly m = new Monopoly();
		m.playOneGame(1);
	}
}
```
