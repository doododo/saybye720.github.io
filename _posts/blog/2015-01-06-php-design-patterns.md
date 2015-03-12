---
layout:		post
title:		开发过程中常用的设计模式
description: PHP常见的设计模式有工厂模式，单例模式，注册器模式等...
category:	blog
---

##单例模式

1.构造函数为非public（防止外部使用new操作符创建对象），单例类不能在其他类中实例化，只能被自身实例化。

2.拥有一个保存类的实例的静态成员变量$_instance;

3.拥有一个访问这个实例的公共静态成员方法

例如：在项目中只需要创建一个数据库链接，那么Database类就需要用单例模式来实现，例子参考最下面的题外。


##工厂模式

比如某个对象在好多个文件中需要实例化操作，如果对象经常发生变化，需要修改每个实例化参数，如果用工厂模式，只需要修改工厂方法中修改即可.

例子：

	//Factory.php
	<?php
		namespace Design;

		class Factory{
			static function createDatabase(){
				//工厂+单例
				$db = Database::getInstance();
				//工厂+注册,将$db注册到全局
				Register::set('db', $db);
				return $db;
			}
		}
	//index.php
	<?php
		
		$db = Design\Factory::createDatabase();

##注册树模式

###题外

如果想在类中实现链式操作，在方法中return $this 即可。

	//Database.php
	<?php
		namespace Design;

		class Database{

			protected $db;
			private function __construct(){

			}

			static function getInstance(){
				if(self::$db){
					return self::db;
				}else{
					self::$db = new self(); //私有化方法，允许本身实例化
					return self::$db;
				}
			}

			function where($where){
				return $this;
			}
			function order($order){
				return $this;
			}
		}
	//index.php
	<?php
		$db = Database::getInstance();  //即使多次调用也只会实例化一次
		$db = \Design\Register::get('db');  //结合注册+工厂+单例，不需要从工厂或者单例中调用
		$rs = $db->where('id = 1')->order('id DESC');

##注册器模式

将一些对象注册到全局的树上面，用来被任何地方直接被访问。

例子：

	//Register.php
	<?php
		namespace Design;

		class Register {
			protected $objects;
			//调用set的时候，把对象注册到树上
			static function set($alias, $object){
				self::$object[$alias] = $object;
			}
			//调用的时候获取对象
			private function get($name){
				return self::$object[$name];
			}

			//调用_unset的时候，把对象从树上移除
			function _unset($alias){
				unset(self::$object[$alias]);
			}
		}

##适配器模式

将不的函数接口封装统一的api，例如，PHP操作mysql有，mysql,mysqli,pdo；缓存有memcache,redis,file,apc等不同的缓存函数，统一成一致的api。

##策略模式

将一组特定的行为和算法封装成类，以适应某些特定的某些上下文。例如电商网站需要针对男性、女性用户跳转不通类目的商品，并且所有广告位展示不同的广告。
	
	//先声明一个策略接口文件
	<?php
		namespace Design;
		interface UserStrategy(){
			//约定2个方法
			function showAd();
			function showCategory(); 
		}

	//FemaleUserStrategy.php 女性用户策略
	<?php
		namespace Design;
		
		class FemaleUserStrategy implemets UserStrategy(){
			function showAd(){
				echo "女装";
			};
			function showCategory(){
				echo "女装频道"
			};
		}
	//MaleUserStrategy.php 男性用户策略
	<?php
		namespace Design;
		
		class MaleUserStrategy implemets UserStrategy(){
			function showAd(){
				echo "肾6";
			};
			function showCategory(){
				echo "电子产品";
			};
		}

	//index.php
	<?php
	class Page{
		protected $strategy
		function index(){
			//这种if else 是传统写法，如果新增还需要每次修改elseif
			if(isset($_GET['female'])){

				}else{

				}
			//这种是策略模式
			$this->strategy = showAd()."<br />";
			$this->strategy = showCategory();
		}
		//策略模式
		function setStrategy(\Desgin\UserStrategy $strategy){
			$this->strategy = $strategy;
		}
	}

	$page = new Page;
	if(isset($_GET['female'])){
		$strategy = new \Desgin\FamaleUserStrategy();
	}else{
		$strategy = new \Desgin\MaleUserStrategy();
	}

`注：如果再新增一种策略，直接新增一个类即可，这样就是实现了解耦。`

##数据对象映射模式

将对象和数据存储映射起来，对一个数据对象的操作映射为对数据存储的操作。实现一个ORM类，将复杂的SQL语句映射为对象属性的操作，让PHP代码看起来更加优雅。

	//新建一张user表，字段 id,name,mobile,create_time
	//User.php
	<?php
	namespace Desgin;
	class User{
		protected $id;
		protected $name;
		protected $mobile;
		protected $create_time;
		protected $db;
		function __construct($id){
			$this->db = \Desgin\Datebase\Mysqli();
			$this->db->connect('127.0.0.1','root','123456','test');
			$res = $this->db->query("select * from user limit 1");
			$data =$res->fetch_assoc();
			$this->id = $data['id'];
			$this->name = $data['name'];
			$this->mobile = $data['mobile'];
			$this->create = $data['create_time'];
		}
		function __destruct($id){

		}

	}


当一个对象状态发生改变时，依赖他的对象全部收到通知，并自动更新。

##面向对象编程的基本原则

1.单一职责：一个类只需要做好一件事

2.开放封闭：一个类可以扩展，而不可修改

3.依赖倒置：一个类不应该强制依赖另一类，每个类对于另外一个类都是可替换的

4.配置化：尽可能的使用配置，而不是硬编码

5.面向接口编程：只需要关心接口，不需要关心实现

