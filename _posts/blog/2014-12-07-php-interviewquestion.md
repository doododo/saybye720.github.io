---
layout:		post
title:		PHP面试过程中常见的问题
category:	blog
description:	关于PHP开发工程师一职位面试过程中常见的问题整理
---

##echo count("abc")输出结果是什么

答案：1，如果abc换为NULL，结果为0

##用PHP写出显示客户端和服务端IP的代码

答案：
	$_SERVER['SERVER_ADDR'] 服务端
	$_SERVER['REMOTE_ADDR'] 客户端

	function getOlineIP(){
		if(getenv('HTTP_CLIENT_IP')) return getenv('HTTP_CLIENT_IP');
		if(getenv('HTTP_X_FORWARDED_FOR')) return getenv('HTTP_X_FORWARDED_FOR');
		if($_SERVER['REMOTE_ADDR']) return $_SERVER['REMOTE_ADDR'];
		if(!empty($HTTP_SERVER_VARS['REMOTE_ADDR'])) return $HTTP_SERVER_VARS['REMOTE_ADDR'];
	};

##error_reporting(2047)有什么作用

答案：PHP显示所有错误 E_ALL

##echo ,print(), print_r()有什么区别

答案：echo是一个语言结构，没有返回值；print()是一个函数，返回int类型值；print_r()是一个函数，返回bool类型值，按结构输出变量的值

##php.ini中的Safe_mode，会影响哪些函数？至少说出6个

答案：1.用户输入、输出函数:fopen(),file(),require();
          2.创建新文件（限制用户只能在用户拥有目录下创建文件）；
          3.用户调用popen(),system(),exec(),只有脚本处在safe_mode_exec_dir配置指令制定的目录中
          4.加强HTTP认证，认证脚本拥有者的UID划入认证脚本范围内，此外启用安全模式下，不会设置PHP_AUTH
          5.MYSQL服务器用户名必须与调用mysql_centect()文件的拥有者用户名相同
          6.受影响的函数变量和配置命令40个左右

##写一个函数解决多进程同时读写一个文件的问题

答案：

	function mGet(){
		$fp = fopen($filename,'r');
		if(flock($fp,LOCK_SH)){
			$rs = fgets($fp,$length);
			flock($fp,LOCK_UN);
		};
		fclose($fp);
		return $rs;
	};
	function mPut(){
		$fp = fopen($filename,'a');
		if(flock($fp,LOCK_EX)){
			fputs($fp,$string);
			flock($fp,LOCK_UN);
		}
		fclose($fp);
	};

##正则验证邮箱

答案：

	preg_match('/^[\w\-\.]+@[\w\-]+(+\.\w+)+$/',$email);

##mysql数据库每天100万的数据增量如何优化数据库

答案：  1.用redis顶住，然后做数据持久化写入
	2.如果是日志类型的，先写到hash队列，然后批量写到数据库
	3.多台数据服务器集群（100万貌似用不到）
	4.分表存储，比如按日期

##写出一种排序算法
	
	<?php

	//插入排序
	function insert_sort($array){
	    $count = count($array);
	    for($i=1; $i<$count; $i++){
	        $tmp = $array[$i];
	        $j = $i-1;
	        while($array[$j] > $tmp){
	            $array[$j+1] = $array[$j];
	            $array[$j] = $tmp;
	            $j--;
	        }
	    }
	    return $array;
	}
	//冒泡排序
	function bubble_sort($array){
	    $count = count($array);
	    if ($count <= 0) return false;
	    for($i=0;$i<$count;$i++){
	        for($j=$count-1;$j>$i;$j--){
	            if($array[$j] < $array[$j-1]){
	                $tmp = $array[$j];
	                $array[$j] = $array[$j-1];
	                $array[$j-1] = $tmp;
	            }
	        }
	    }
	    return $array;
	}

	$array = [10,11,9,6,5,1,0,12];
	var_dump(bubble_sort($array));

##写个函数用来对二维数组排序

	function extname1($path){
    	return strrchr($path, ".");
	}
	function extname2($path){
	    $arr = explode(".", $path);
	    return $arr[count($arr)-1];
	}
	function extname3($path){
	    $postion = strrpos($path, ".");
	    return substr($path,$postion);
	}

	$path = __FILE__;
	echo extname3($path);
	//还可以用正则

##数据库中的事务是什么?

事务是作为一个单元的一组有序的数据库操作。如果组中的所有操作都成功，则认为事务成功，即使只有一个操作失败，事务也不成功。如果所有操作完成，事务则提交，其修改将作用于所有其他数据库进程。如果一个操作失败，则事务将回滚，该事务所有操作的影响都将取消

##PHP格式化打印2014-5-10 22:21:21

	echo date('Y-m-d H:i:s', strtotime('-1 days'));


##选猴子王问题

问题：一群猴子排成一圈，按1，2，...，n依次编号。然后从第1只开始数，数到第m只,把它踢出圈，从它后面再开始数，再数到第m只，在把它踢出去...，如此不停的进行下去，直到最后只剩下一只猴子为止，那只猴子就叫做大王。要求编程模拟此过程，输入m、n, 输出最后那个大王的编号。

（逻辑我还没想清楚）

